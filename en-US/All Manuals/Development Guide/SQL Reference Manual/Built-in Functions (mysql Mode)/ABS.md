```ebnf+diagram
abs::= ABS "(" expr ")"
```
The ABS function calculates the absolute value of [expr](../General SQL Syntax/expr).

The mapping relationship between input parameter types and return types is as follows:

|Parameter Type |Return Value |
|------------------|--------|
| TINYINT          | INT    |
| SMALLINT         | INT    |
| INT              | INT    |
| BOOL             | INT    |
| BIGINT           | BIGINT |
| TINYINT UNSIGNED | INT UNSIGNED   |
| SMALLINT UNSIGNED        |  INT UNSIGNED      |
| MEDIUMINT UNSIGNED       |  BIGINT UNSIGNED     |
| INT UNSIGNED             |  BIGINT UNSIGNED     |
| BIGINT UNSIGNED          |  BIGINT UNSIGNED     |
| BIT              | BIGINT |
| NUMBER           | NUMBER |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| VARCHAR          | DOUBLE |
| NCHAR            | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| BINARY           | DOUBLE |

**expr**

[General expression](../General SQL Syntax/expr), the data type of its value is as shown in the input parameter types in the table above.

- When the value of expr is of date type, the date type will be converted to specific year, month, and day numerical values before the absolute value calculation.

- When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ABS(-2.345) abs1, 
ABS(1/10) abs2, 
ABS(1-2*3) abs3, 
ABS(TO_NUMBER('$3.33','$9.99')) abs4 
FROM DUAL;
 
       ABS1        ABS2                  ABS3        ABS4
----------- ----------- --------------------- -----------
      2.345          .1                     5        3.33
```
