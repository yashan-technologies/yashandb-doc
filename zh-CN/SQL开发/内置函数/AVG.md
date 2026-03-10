```ebnf+diagram
avg::= AVG "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

AVG函数计算给定参数的平均值，返回值的类型有以下几种情况：

|  参数类型| 返回值|
| --- | --- |
| TINYINT, SMALLINT, INT, BIGINT, NUMBER | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | DOUBLE |
| CHAR | NUMBER |
| VARCHAR | NUMBER |
| NCHAR | NUMBER |
| NVARCHAR | NUMBER |

其中[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

在单行计算中，当expr的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略expr值为空的行，当所有行均为空时，计算结果为NULL。

该聚集函数不可与group_id、grouping、grouping_id以及UDF聚集函数嵌套，expr可以为其他聚集函数或者其他通用表达式，聚集函数嵌套层数不能超过一层。

**DISTINCT**

表示在计算平均值时，过滤掉重复的行。

**ALL**

默认值，表示对所有行计算平均值。

示例

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME            DHQ             EMPLOYEE_COUNT
------- -------------------- --------------- --------------
01      EastChina               Shanghai                      
02      WestChina               Chengdu                    300
03      SouthChina               Guangzhou                  400
04      NorthChina               Beijing                    300
05      CentralChina               Wuhan                           
  
-- 计算平均员工数，员工数为空的行在计算时被忽略
SELECT AVG(employee_count) res FROM area1;
                RES
-------------------
         333.333333
  
-- 计算平均员工数（过滤掉重复值），员工数为空的行在计算时被忽略
SELECT AVG(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
                 350
--嵌套聚集
SELECT AVG(AVG(employee_count)) res FROM area1 GROUP BY employee_count;      
        RES
-----------
          0
```

**OVER**

当指定OVER关键字时，AVG将作为[窗口函数](../../全部手册/开发手册/SQL参考手册/内置函数（yashan模式）/00内置函数（yashan模式）.html#WindowFunction)，并支持滑动窗口，返回多行的平均值。

**analytic_clause**

窗口函数通用语法，详情请查阅[窗口函数](../../全部手册/开发手册/SQL参考手册/内置函数（yashan模式）/00内置函数（yashan模式）.html#WindowFunction)。

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

SELECT year,month,
revenue_total curr,
AVG(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH        CURR       TONOW 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          28888       28888
2021  01          38888       33888
2021  02          37778  35184.6667

--分年统计每月所有机构的平均收入,及年初至今所有机构的平均收入
SELECT year,month,
AVG(revenue_total) curr,
AVG(AVG(revenue_total)) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info
GROUP BY year,month;
YEAR  MONTH        CURR       TONOW 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          33888       33888
2021  02          37778       35833
```

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](../../全部手册/开发手册/SQL参考手册/内置函数（yashan模式）/00内置函数（yashan模式）.html#KeepAggregateFunction)。

使用KEEP子句时AVG函数无法使用DISTINCT。

KEEP聚集函数仅适用于HEAP表。

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

--统计最早的年数的薪资的平均值
SELECT AVG(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

AVG(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                              2888

--统计最晚的年数的薪资的平均值
SELECT AVG(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

AVG(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                       35184.6667
```
