```ebnf+diagram
count::= COUNT "(" ("*"|([DISTINCT|ALL] expr)) ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

COUNT函数按给定参数[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)统计记录行数，本函数返回一个BIGINT的数值，且永远不会返回NULL。

在单行计算中，当expr的值为NULL时，函数返回0。

在多行计算中，当expr为字面量、变量、常量时，函数统计的是所有行，包括空行。否则，函数将忽略expr值为空的行，当所有行均为空时，计算结果为0。

该聚集函数不可与group_id、grouping、grouping_id以及UDF聚集函数嵌套，expr可以为其他聚集函数或者其他通用表达式，聚集函数嵌套层数不能超过一层，其类型可以是UDT以外的任意数据类型。

**\***

表示统计所有的行，包括空行。

**DISTINCT**

表示将表达式结果进行重复过滤，统计的是所有不重复的非空行。


目前，YashanDB无法对LOB类型的数据进行去重。


**ALL**

默认值，表示不对表达式结果进行重复过滤，统计的是所有非空行。

示例

```sql
--统计机构表的行数
SELECT COUNT(1) res FROM branches;
                  RES
---------------------
                   12
  
--统计机构表中area_no字段非空的行数,该语句与SELECT COUNT(ALL area_no) FROM branches查询结果一致
SELECT COUNT(area_no) res FROM branches;
                  RES
---------------------
                   10
  
--统计机构表中area_no字段非空且不值不相同的行数
SELECT COUNT(DISTINCT area_no) res FROM branches;
                  RES
---------------------
                    4

-- 嵌套聚集
SELECT COUNT(COUNT(employee_count)) res FROM area1 GROUP BY employee_count;    
                  RES
---------------------
                    3
```

**OVER**

当指定OVER关键字时，COUNT将作为[窗口函数](00内置函数.html#WindowFunction)，并支持滑动窗口，返回多行的统计行数。

**analytic_clause**

窗口函数通用语法。

示例

```sql
--finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

--分年统计每月存在收入的机构数
SELECT year,month,
COUNT(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH                 TOALL
----- ----- --------------------- 
2001  01                        1
2021  01                        2
2021  01                        2
2021  02                        3

--分年统计每月年初至今存在收入的机构数
SELECT year,month,
COUNT(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH                 TONOW 
----- ----- --------------------- 
2001  01                        1
2021  01                        1
2021  01                        2
2021  02                        3
```

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](00内置函数.html#KeepAggregateFunction)。

使用KEEP子句时COUNT函数无法使用DISTINCT。

示例

```sql
--finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

--统计最早的年数的薪资的总值
SELECT COUNT(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

COUNT(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
----------------------------------------------------
                                                   6

--统计最晚的年数的薪资的总值
SELECT COUNT(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

COUNT(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
---------------------------------------------------
                                                 18
```
