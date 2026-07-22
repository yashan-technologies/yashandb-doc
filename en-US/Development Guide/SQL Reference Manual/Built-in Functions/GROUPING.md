```ebnf
grouping = GROUPING "(" expr ")".
```
The GROUPING function is used to mark whether the field [expr](../General SQL Syntax/expr) is the key value of the group for each row in a grouped aggregate result.

The return type of this function is NUMBER, where an output value of 0 indicates that the field is a key value, and an output value of 1 indicates that the field is not a key value.

This function must be used in conjunction with the GROUP BY [expr](../General SQL Syntax/expr)/ROLLUP/CUBE/GROUPING SETS statements, and DISTINCT cannot be used within the function.

The value of expr can be any data type except UDT, it cannot be NULL and cannot be an aggregate function.

***Example***

```sql
SELECT GROUPING(year) res FROM finance_info GROUP BY GROUPING SETS(year, month);

        RES
-----------
          1
          1
          0
          0

SELECT GROUPING(year) res FROM finance_info GROUP BY ROLLUP(year, month);

        RES
-----------
          0
          0
          0
          0
          0
          1
```
