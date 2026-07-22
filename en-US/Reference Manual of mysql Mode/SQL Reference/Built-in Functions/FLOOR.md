```ebnf
floor = FLOOR "(" expr ")".
```
The FLOOR function rounds down the [expr](../General SQL Syntax/expr).

The mapping of parameter types to return types is as follows:

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

[General expression](../General SQL Syntax/expr), with the data types of its values as indicated in the parameter type table above.

- When the value of expr is a date type, it will convert the date type to specific year, month, and day values and perform rounding down calculations.

- When the value of expr is NULL, the function will return NULL.

- The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT FLOOR(7) floor1,
FLOOR('1.97') floor2,
FLOOR(6.22) floor3
FROM DUAL;

      floor1      floor2                floor3
------------ ----------- ---------------------
           7    1.0E+000                     6

```
