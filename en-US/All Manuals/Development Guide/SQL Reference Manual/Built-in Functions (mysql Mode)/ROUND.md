```ebnf+diagram
round::= ROUND "("expr ["," round_number]")"
```

The ROUND function rounds the [expr](../General SQL Syntax/expr) according to the specified number of decimal places in round_number, returning the result of the computation.

The mapping relationship between input parameter types and return types is as follows:

|Input Parameter Type |Return Type |
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
| BIGINT UNSIGNED            | BIGINT UNSIGNED    |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| NCHAR            | DOUBLE |
| VARCHAR          | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| BINARY           | DOUBLE |
| NUMBER           | NUMBER |

**expr**

[General expression](../General SQL Syntax/expr), the data types of its values are as shown in the input parameter types table above.

- When the value of expr is of date type, it converts the date type into specific year, month, and day numeric values and then performs the rounding calculation at the specified decimal places.

- If either expr or round_number is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ROUND(7.11) round1,
       ROUND(10.97) round2,
       ROUND(4.55,5) round3
FROM DUAL;
round1      round2      round3 
----------- ----------- ----------- 
          7          11        4.55
```
