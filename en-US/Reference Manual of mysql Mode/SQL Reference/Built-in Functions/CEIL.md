```ebnf
ceil = CEIL "(" expr ")".
```
The CEIL function rounds up the [expr](../General SQL Syntax/expr).

The mapping relationship between parameter types and return types is as follows:

|Parameter Type |Return Value |
|-----------|-----|
| TINYINT   | INT |
| SMALLINT  | INT |
| INT       | INT |
| BOOL      | INT |
| BIT       | BIGINT |
| NUMBER    | NUMBER |
| BIGINT    | NUMBER |
| TINYINT UNSIGNED | BIGINT UNSIGNED   |
| SMALLINT UNSIGNED        |  BIGINT UNSIGNED      |
| MEDIUMINT UNSIGNED        |  BIGINT UNSIGNED      |
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

[General Expression](../General SQL Syntax/expr), where the data types of its values are as indicated in the parameter types table above.

- When the value of expr is of date type, the date type is converted to specific year, month, and day values, and then the rounding up calculation is performed.

- When the value of expr is NULL, the function returns NULL.

- The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CEIL(7) ceil1,
CEIL('0.97') ceil2,
CEIL(6.22) ceil3
FROM DUAL;
       ceil1       ceil2       ceil3
------------ ----------- -----------
           7    1.0E+000           7
```
