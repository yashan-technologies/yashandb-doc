```ebnf+diagram
grouping_id::= GROUPING_ID "(" (expr) {"," (expr)} ")"
```
The GROUPING_ID function returns the decimal value corresponding to the binary GROUPING result of all parameters [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr).

The return type of this function is NUMBER. When there is only one parameter [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) of GROUPING_ID, the result is the same as GROUPING.

This function supports 1 to 126 parameters. An error will be reported if the number of parameters is outside this range.

This function must be used in conjunction with the GROUP BY [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)/ROLLUP/CUBE/GROUPING SETS statements, and DISTINCT cannot be used within the function. The [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) can be any data type except UDT, cannot be NULL, and cannot be an aggregate function.

***Example***

```sql
SELECT GROUPING_ID(year) res FROM finance_info GROUP BY GROUPING SETS(year, month);

        RES
-----------
          1
          1
          0
          0

SELECT GROUPING_ID(year, month) res FROM finance_info GROUP BY GROUPING SETS(year, month);

        RES
-----------
          2
          2
          1
          1
```
