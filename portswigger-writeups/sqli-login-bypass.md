# SQL Injection Vulnerability Allowing Login Bypass

## Objective
Exploit a SQL injection vulnerability in the login function to log in as the `administrator` user.

## Vulnerable Parameter
`username`

## Original Query
```sql
SELECT * FROM users WHERE username = 'administrator' AND password = 'can_be_anything'
```

## Payload Used
```sql
administrator'--
```

## Explanation
The payload closes the username string and uses the SQL comment operator `--` to ignore the remainder of the query.

Modified query:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'can_be_anything'
```

The password condition becomes commented out, so the database only checks whether the username is `administrator`.

As a result, authentication is bypassed without knowing the correct password.

## Prevention
- Use parameterized queries / prepared statements.
- Avoid directly concatenating user input into SQL queries.
- Apply proper server-side validation.

