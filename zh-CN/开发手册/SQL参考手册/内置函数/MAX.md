```ebnf
max = MAX "(" [DISTINCT|UNIQUE|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"].

keep_clause = KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")".

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

MAX函数计算给定参数[expr](../通用SQL语法/expr)的最大值。

当USE_NATIVE_TYPE为TRUE时其返回值类型与参数的类型一致；当USE_NATIVE_TYPE为FALSE时，除FLOAT类型返回为NUMBER外，其余返回值类型与参数的类型一致。

在单行计算中，当expr的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略expr值为空的行，当所有行均为空时，计算结果为NULL。

该聚集函数不可与group_id、grouping、grouping_id以及UDF聚集函数嵌套，expr可以为其他聚集函数或者其他通用表达式，聚集函数嵌套层数不能超过一层，其数据类型可以是除布尔型、大对象型、JSON及UDT以外的数据类型。

**DISTINCT|UNIQUE**

表示在计算最大值时，过滤掉重复的行。

DISTINCT与UNIQUE完全等价。

**ALL**

默认值，表示对所有行计算最大值。

示例

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME              DHQ           EMPLOYEE_COUNT
------- ---------------------- ------------- --------------
01      EastChina                 Shanghai                    
02      WestChina                 Chengdu                  300
03      SouthChina                 Guangzhou                400
04      NorthChina                 Beijing                  300
05      CentralChina                 Wuhan                       
  
-- 计算最大的员工数量，与SELECT MAX(DISTINCT employee_count) FROM area1语句的查询结果一致
SELECT MAX(employee_count) res FROM area1;
         RES 
------------ 
         400

SELECT MAX(true) FROM sys.dual;

[1:12]YAS-08021 invalid data type: BOOLEAN

-- 嵌套聚集
SELECT MAX(COUNT(employee_count)) res FROM area1 GROUP BY employee_count;

                  RES
---------------------
                    2
```

**OVER**

当指定OVER关键字时，MAX将作为[窗口函数](00内置函数.md#WindowFunction)，并支持滑动窗口，返回多行的最大值。

**analytic\_clause**

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

-- 分年统计每月的最高收入
SELECT year,month,
MAX(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH       TOALL 
----- ----- ----------- 
2001  01           2888
2021  01          38888
2021  01          38888
2021  02          38888

-- 分年统计每月年初至今的最高收入
SELECT year,month,
MAX(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH       TONOW 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          38888
2021  02          38888
```

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](00内置函数.md#KeepAggregateFunction)。

使用KEEP子句时MAX函数无法使用DISTINCT。

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

--统计最早的年数的薪资的最大值
SELECT MAX(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

MAX(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                              2888

--统计最晚的年数的薪资的最大值
SELECT MAX(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

MAX(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                            38888
```
