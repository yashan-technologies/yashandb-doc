```ebnf
truncate = TRUNCATE "(" expr "," n ")".
```

The TRUNCATE function truncates the value of [expr](../General SQL Syntax/expr) to `n` decimal places, discarding any excess digits without rounding.

**expr**

A general expression whose value must conform to the following data type requirements and corresponding return type mappings:

|Type of expr |Return Type |
|------------------|--------|
| TINYINT          | INT    |
| SMALLINT         | INT    |
| INT              | INT    |
| BOOL             | INT    |
| BIT              | BIGINT    |
| BIGINT           | BIGINT |
| TINYINT UNSIGNED | BIGINT UNSIGNED    |
| SMALLINT UNSIGNED        | BIGINT UNSIGNED    |
| MEDIUMINT UNSIGNED       | BIGINT UNSIGNED    |
| INT UNSIGNED             | BIGINT UNSIGNED    |
| BIGINT UNSIGNED          | BIGINT UNSIGNED |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| NCHAR            | DOUBLE |
| VARCHAR          | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| NUMBER           | NUMBER |

- When used to truncate numeric types, the value of expr must be a numeric type or other types that can be converted to NUMBER (conversion failure will return an Invalid number error).

- When the value of expr is a date type, the date type will be converted to specific year, month, and day numeric values for truncation.

- When the value of expr is NULL, the function returns NULL.

**n**

Specifies the truncation position, and must be a numeric type or other types that can be converted to a numeric value.



The specific truncation rules are as follows:

|n Value |Function Behavior |Example |
| --- | --- | --- |
| n is a decimal | Considers the integer part of n for numerical truncation | TRUNCATE(123.456, 1.6) = TRUNCATE(123.456, 1) <br/> TRUNCATE(123.456, -1.2) = TRUNCATE(123.456, -1) |
| n = 0   | Truncates the decimal part            | TRUNCATE(123.456, 0) = 123                  |
| n > 0 and within number_expr decimal places | Truncates the part beyond that decimal place | TRUNCATE(123.456, 2) = 123.45               |
| n > 0 and exceeding number_expr decimal places | Returns the original data                   | TRUNCATE(123.456, 5) = 123.456               |
| n < 0 and within number_expr integer places | Truncates the decimal part, and sets the integer part to 0 from right to left for n (absolute value) digits | TRUNCATE(123.456, -1) = 120 <br/> TRUNCATE(123.456, -2) = 100 |
| n < 0 and exceeding number_expr integer places | Returns 0       | TRUNCATE(123.456, -5) = 0             |
| n is NULL    | Returns NULL                           |          |



***Example*** for Standalone Deployment Heap tables

```sql
SELECT TRUNCATE(1.123, 1) res FROM DUAL;
res                   
--------------------- 
1.1

SELECT TRUNCATE(18.123, -1) res FROM DUAL;
res
--------------------- 
10
    
SELECT TRUNCATE(null, 1) res FROM DUAL;
res       
--------- 
NULL 
```
