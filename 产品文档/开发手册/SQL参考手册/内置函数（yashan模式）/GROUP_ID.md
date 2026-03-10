```ebnf+diagram
group_id::= GROUP_ID "("")"

```
GROUP_ID函数用于返回重复的分组。不同的分组结果会以0进行标识，相同的分组结果会在0的基础上递增。

本函数无入参，返回类型为INT。

本函数必须与GROUP BY [expr](../通用SQL语法/expr)/ROLLUP/CUBE/GROUPING SETS语句结合使用。

本函数仅适用于列存表。

示例（LSC表、TAC表）

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
