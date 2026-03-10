```ebnf+diagram
max::= MAX "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The MAX function calculates the maximum value of the given parameter [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr).

When USE_NATIVE_TYPE is TRUE, the return value type is consistent with the parameter type; when USE_NATIVE_TYPE is FALSE, except for FLOAT type which returns as NUMBER, other return value types are consistent with the parameter type.

In single-row calculations, if the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the expr value is empty, and when all rows are empty, the calculation result will be NULL.

This aggregate function cannot be nested with group_id, grouping, grouping_id, and UDF aggregate functions. The expr can be other aggregate functions or other general expressions. The nesting level of aggregate functions cannot exceed one level, and its data types can be any type except Boolean, large object, JSON, and UDT.

**DISTINCT**

Indicates that duplicate rows are filtered out when calculating the maximum value.

**ALL**

The default value, indicating that the maximum value is calculated for all rows.

***Example***

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME              DHQ           EMPLOYEE_COUNT
------- ---------------------- ------------- --------------
01      EastChina                 Shanghai                    
02      WestChina                 Chengdu                  300
03      SouthChina                 Guangzhou                400
04      NorthChina                 Beijing                  300
05      CentralChina                 Wuhan                       
  
-- Calculate the maximum employee count, consistent with the result of SELECT MAX(DISTINCT employee_count) FROM area1
SELECT MAX(employee_count) res FROM area1;
         RES 
------------ 
         400

SELECT MAX(true) FROM sys.dual;

[1:12]YAS-08021 invalid data type: BOOLEAN

-- Nested aggregation
SELECT MAX(COUNT(employee_count)) res FROM area1 GROUP BY employee_count;

                  RES
---------------------
                    2
```

**OVER**

When the OVER keyword is specified, MAX acts as a [window function](../../All Manuals/Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode).html#WindowFunction) and supports sliding windows, returning the maximum value of multiple rows.

**analytic_clause**

General syntax of window functions.

***Example***

```sql
-- The finance_info table records the revenue situation by year, month, and institution
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Statistics of the highest revenue per month by year
SELECT year,month,
MAX(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH       TOALL 
----- ----- ----------- 
2001  01           2888
2021  01          38888
2021  01          38888
2021  02          38888

-- Statistics of the highest revenue from the beginning of the year to date
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

KEEP aggregate function general syntax, for details please refer to [KEEP aggregate function](../../All Manuals/Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode).html#KeepAggregateFunction).

When using the KEEP clause, the MAX function cannot use DISTINCT.

***Example***

```sql
-- The finance_info table records the revenue situation by year, month, and institution
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Count the maximum salary of the earliest year
SELECT MAX(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

MAX(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                              2888

-- Count the maximum salary of the latest year
SELECT MAX(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

MAX(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                            38888
```
