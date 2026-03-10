```ebnf+diagram
sum::= SUM "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

SUM函数计算给定参数[expr](../通用SQL语法/expr)的数值和，其返回值类型如下：

|  expr类型| 返回值|
| --- | --- |
| TINYINT, SMALLINT | BIGINT |
| INT, BIGINT | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | DOUBLE |
| NUMBER | NUMBER |

当参数为其他类型且无法转换为上述类型时，不执行计算并返回类型转换错误或是类型不支持。

在单行计算中，当expr的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略expr值为空的行，当所有行均为空时，计算结果为NULL。

该聚集函数不可与group_id、grouping、grouping_id以及UDF聚集函数嵌套，expr可以为其他聚集函数或者其他通用表达式，聚集函数嵌套层数不能超过一层，其类型只能是数值型、字符型。

**DISTINCT**

表示在计算数值和时，过滤掉重复的行。

**ALL**

默认值，表示对所有行计算数值和。

示例

```sql
-- branches1表中包含了区域信息、各区员工数量等
  
-- 分区域计算员工总数量，对01、04区域对应员工数量为空的行在计算时被忽略，对02区域对应员工数量只有一行且值为空，此时计算返回NULL结果
SELECT area_no,SUM(employee_count) sum FROM branches1 GROUP BY area_no;
AREA_NO                 SUM
------- -------------------
                         70
01                       70
04                       40
02                       
05                       40
  
-- 分区域计算一个常量的数值和
SELECT area_no,SUM(10) sum FROM branches1 GROUP BY area_no;
AREA_NO         SUM
------- -----------
                 20
01               40
04               40
02               10
05               10

-- 嵌套聚集
SELECT SUM(SUM(employee_count)) res FROM area1 GROUP BY employee_count;
        RES
-----------
       1000
```

**OVER**

当指定OVER关键字时，SUM将作为[窗口函数](00内置函数.html#WindowFunction)，并支持滑动窗口，返回多行的数值和。

**analytic_clause**

窗口函数通用语法。

示例

```sql
-- finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

SELECT year,month,
revenue_total curr,
SUM(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH        CURR       TONOW 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          28888       28888
2021  01          38888       67776
2021  02          37778      105554

-- 分年统计每月所有机构的收入和,及年初至今所有机构的收入和
SELECT year,month,
SUM(revenue_total) curr,
SUM(SUM(revenue_total)) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info
GROUP BY year,month;
YEAR  MONTH        CURR       TONOW 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          67776       67776
2021  02          37778      105554
```

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](00内置函数.html#KeepAggregateFunction)。

使用KEEP子句时SUM函数无法使用DISTINCT。

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

--统计最早的年数的薪资的总和
SELECT SUM(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

SUM(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                             17328

--统计最晚的年数的薪资的总和
SELECT SUM(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

SUM(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                           633324
```
