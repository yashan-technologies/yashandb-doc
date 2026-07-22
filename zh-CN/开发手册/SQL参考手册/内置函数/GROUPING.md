```ebnf
grouping = GROUPING "(" expr ")".
```

在GROUP BY分组排序计算时，可以通过GROUPING函数确定查询结果是否为聚合结果，通常用于ROLLUP、CUBE或GROUPING SETS等拓展聚合计算场景。

本函数的返回值类型为NUMBER，输出结果为0表示该字段为键值，输出结果为1则表示该字段是聚合结果。

本函数必须与`GROUP BY`、`GROUP BY ROLLUP`、`GROUP BY CUBE`或`GROUP BY GROUPING SETS`语句结合使用，函数内部不能使用DISTINCT。

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
