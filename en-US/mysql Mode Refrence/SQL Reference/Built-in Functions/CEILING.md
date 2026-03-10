```ebnf+diagram
ceiling::= CEILING "(" expr ")"
```
The CEILING function returns the result of rounding up the [expr](../General SQL Syntax/expr). This function is a synonym for [CEIL](CEIL).

The mapping of input parameter types to return types is as follows:

|Parameter Type |Return Value |
|-----------|-----|
| TINYINT   | INT |
| SMALLINT  | INT |
| INT       | INT |
| BOOL      | INT |
| BIT       | BIGINT |
| NUMBER    | BIGINT |
| BIGINT    | NUMBER |
| TINYINT UNSIGNED | BIGINT UNSIGNED   |
| SMALLINT UNSIGNED        |  BIGINT UNSIGNED      |
| MEDIUMINT UNSIGNED       |  BIGINT UNSIGNED      |
| INT UNSIGNED             |  BIGINT UNSIGNED     |
| BIGINT UNSIGNED          |  BIGINT UNSIGNED     |
| FLOAT     | DOUBLE |
| DOUBLE    | DOUBLE |
| CHAR      | DOUBLE |
| VARCHAR   | DOUBLE |
| NCHAR     | DOUBLE |
| NVARCHAR  | DOUBLE |
| DATE      | DOUBLE |
| TIME      | DOUBLE |
| TIMESTAMP | DOUBLE |
| BINARY | DOUBLE |

**expr**

[General expression](../General SQL Syntax/expr), the data types of its values are as shown in the input parameter types table above.

- When the value of expr is of date type, the date type is converted to specific year, month, and day numeric values before rounding up.

- When the value of expr is NULL, the function returns NULL.

- The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CEILING(7) ceil1,
CEILING('0.97') ceil2,
CEILING(6.22) ceil3
FROM DUAL;
       ceil1       ceil2       ceil3
------------ ----------- -----------
           7    1.0E+000           7
```
