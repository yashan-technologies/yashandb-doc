```ebnf
grouping = GROUPING "(" expr ")".
```

When performing grouping and sorting calculations with GROUP BY, you can use the GROUPING function to determine whether the query result is a aggregation marker or not, and it typically used in extended aggregate computation scenarios such as ROLLUP, CUBE, or GROUPING SETS. In the GROUPING result, a binary bit set to 1 indicates that the row record is an aggregated result for the corresponding query column in order; a bit set to 0 indicates a normal table record. 

The return type of this function is NUMBER, where an output value of 0 indicates that the field is a row value, and an output value of 1 indicates that the field is an aggregated result.

This function must be used in conjunction with the `GROUP BY`, `GROUP BY ROLLUP`, `GROUP BY CUBE`, or `GROUP BY GROUPING SETS` statements, and DISTINCT cannot be used within the function.

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
