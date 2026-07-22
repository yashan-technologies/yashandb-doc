```ebnf
group_id = GROUP_ID "("")".
```
The GROUP_ID function is used to return repeated groups. Different group results are identified by 0, while identical group results increment on top of 0.

This function has no parameters and returns a type of INT.

This function must be used in conjunction with the GROUP BY [expr](../General SQL Syntax/expr)/ROLLUP/CUBE/GROUPING SETS statements.

***Example***

```sql
SELECT year, month, GROUP_ID() res FROM finance_info GROUP BY GROUPING SETS(year, year, month);

YEAR  MONTH          RES
----- ----- ------------
      01               0
      02               0
2001                   0
2021                   0
2001                   1
2021                   1

SELECT year, month, GROUP_ID() res FROM finance_info GROUP BY ROLLUP(year, year, month);

YEAR  MONTH          RES
----- ----- ------------
2001  01               0
2021  01               0
2021  02               0
2001                   0
2021                   0
2001                   1
2021                   1
                       0
```
