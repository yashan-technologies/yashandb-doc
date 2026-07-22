```ebnf
avg = AVG "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"].

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

AVG函数计算给定参数的平均值。

入参类型与返回类型映射关系如下：

|  参数类型| 返回值|
|-------------------------------------------------------------------------------------------------------|--------|
| TINYINT, SMALLINT, INT, BIGINT, NUMBER, BOOL, BIT, TINYINT UNSIGNED, SMALLINT UNSIGNED, INT UNSIGNED, BIGINT UNSIGNED | NUMBER |
| DOUBLE, FLOAT, VARCHAR, CHAR, NVARCHAR, NCHAR, BINARY                                                       | DOUBLE |

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

在单行计算中，当expr的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略expr值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**DISTINCT**

表示在计算平均值时，过滤掉重复的行。

**ALL**

默认值，表示对所有行计算平均值。

示例（HEAP表）

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
area_no area_name            DHQ             employee_count
------- -------------------- --------------- --------------
01      EastChina               Shanghai                      
02      WestChina               Chengdu                    300
03      SouthChina               Guangzhou                  400
04      NorthChina               Beijing                    300
05      CentralChina               Wuhan                           
  
-- 计算平均员工数，员工数为空的行在计算时被忽略
SELECT AVG(employee_count) res FROM area1;
                res
-------------------
         333.333333
  
-- 计算平均员工数（过滤掉重复值），员工数为空的行在计算时被忽略
SELECT AVG(DISTINCT employee_count) res FROM area1;
                 res
--------------------
                 350
```

**OVER**

当指定OVER关键字时，AVG将作为[窗口函数](../../../开发手册/SQL参考手册/内置函数/00内置函数.md#WindowFunction)，并支持滑动窗口，返回多行的平均值。

**analytic_clause**

窗口函数通用语法，详情请查阅[窗口函数](../../../开发手册/SQL参考手册/内置函数/00内置函数.md#WindowFunction)。

示例（HEAP表）

```sql
--finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
year  month branch revenue_total 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

SELECT year,month,
revenue_total curr,
AVG(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
year  month        curr       tonow 
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
year  month        curr       tonow 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          33888       33888
2021  02          37778       35833
```
