```ebnf+diagram
grouping_id::= GROUPING_ID "(" (expr) {"," (expr)} ")"

```
GROUPING_ID函数返回所有参数[expr](../通用SQL语法/expr)的二进制GROUPING结果所对应的十进制数值。

本函数的返回值类型为NUMBER，当GROUPING_ID的参数[expr](../通用SQL语法/expr)只有1个时，结果同GROUPING。

本函数支持1 - 126个参数，参数数量不在此范围则报错。

本函数必须与GROUP BY [expr](../通用SQL语法/expr)/ROLLUP/CUBE/GROUPING SETS语句结合使用，函数内部不能使用DISTINCT。[expr](../通用SQL语法/expr)可以为除UDT以外的任意数据类型，不能为NULL且不能为聚集函数。

本函数仅适用于列存表。

示例（TAC表、LSC表）

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
