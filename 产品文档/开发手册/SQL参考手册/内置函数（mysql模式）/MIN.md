```ebnf+diagram
min::= MIN "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"]

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

MIN函数计算给定参数[expr](../通用SQL语法/expr)的最小值。

入参类型与返回类型一致。

在单行计算中，当expr的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略expr值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式，其数据类型可以是除布尔型、大对象型、JSON、XMLTYPE及UDT以外的数据类型。

**DISTINCT**

表示在计算最小值时，过滤掉重复的行。

**ALL**

默认值，表示对所有行计算最小值。

示例（单机HEAP表）

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME             DHQ               EMPLOYEE_COUNT
------- --------------------- ----------------- --------------
01      华东                Shanghai                        
02      华西                Chengdu                      300
03      华南                Guangzhou                    400
04      华北                Beijing                      300
05      华中                Wuhan                           
  
-- 计算最小的员工数量，与SELECT MIN(DISTINCT employee_count) FROM area1语句的查询结果一致
SELECT MIN(employee_count) res FROM area1;
         RES 
------------ 
         300

SELECT MIN(true) FROM sys.dual;

MIN(true) 
--------- 
        1
```

**OVER**

当指定OVER关键字时，MIN将作为[窗口函数](../内置函数（yashan模式）/00内置函数（yashan模式）.html#WindowFunction)，并支持滑动窗口，返回多行的最小值。

**analytic_clause**

窗口函数通用语法，详情请查阅[窗口函数](../内置函数（yashan模式）/00内置函数（yashan模式）.html#WindowFunction)。

示例（单机HEAP表）

```sql
-- finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- 分年统计每月的最低收入
SELECT year,month,
    MIN(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH       TOALL 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          28888
2021  02          28888

-- 分年统计每月年初至今的最低收入
SELECT year,month,
    MIN(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH       TONOW 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          28888
2021  02          28888
```
