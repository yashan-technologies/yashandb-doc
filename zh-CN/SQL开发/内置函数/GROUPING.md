```ebnf+diagram
grouping::= GROUPING "(" expr ")"
```
GROUPING函数用于在一个分组聚合结果的每一行中标记字段[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)是否为该行所在分组的键值。

本函数的返回值类型为NUMBER，输出结果为0表示该字段为键值，输出结果为1则表示该字段不是键值。

本函数必须与GROUP BY [expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)/ROLLUP/CUBE/GROUPING SETS语句结合使用，函数内部不能使用DISTINCT。

expr的值可以为除UDT以外的任意数据类型，expr不能为NULL且不能为聚集函数。

示例

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
