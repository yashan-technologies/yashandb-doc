```ebnf+diagram
min::= MIN "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The MIN function calculates the minimum value of the given parameter [expr](../General SQL Syntax/expr).

When USE_NATIVE_TYPE is TRUE, the return type is consistent with the type of the parameter; when USE_NATIVE_TYPE is FALSE, except for the FLOAT type returning as NUMBER, other return types are consistent with the type of the parameter.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the expr value is NULL. If all rows are NULL, the result of the calculation will be NULL.

This aggregate function cannot be nested with group_id, grouping, grouping_id, and UDF aggregate functions. The expr can be other aggregate functions or other general expressions. The nesting level of aggregate functions cannot exceed one layer. Its data type can be any type except for BOOLEAN, large object types, JSON, and UDT.

**DISTINCT**

Indicates that duplicate rows are filtered out when calculating the minimum value.

**ALL**

The default value, indicating that the minimum value is calculated over all rows.

***Example***

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME             DHQ               EMPLOYEE_COUNT
------- --------------------- ----------------- --------------
01      EastChina                Shanghai                        
02      WestChina                Chengdu                      300
03      SouthChina                Guangzhou                    400
04      NorthChina                Beijing                      300
05      CentralChina                Wuhan                           
  
-- Calculate the minimum employee count, consistent with the result of SELECT MIN(DISTINCT employee_count) FROM area1
SELECT MIN(employee_count) res FROM area1;
         RES 
------------ 
         300

SELECT MIN(true) FROM sys.dual;

[1:12]YAS-08021 invalid data type: BOOLEAN

-- Nested aggregation
SELECT MIN(COUNT(employee_count)) res FROM area1 GROUP BY employee_count;

                  RES
---------------------
                    0
```

**OVER**

When the OVER keyword is specified, MIN will act as a [window function](00Built-in Functions (yashan Mode).html#WindowFunction) and supports sliding windows, returning the minimum value across multiple rows.

**analytic_clause**

General syntax of window functions.

***Example***

```sql
-- The finance_info table records annual, monthly, and institutional revenue
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Calculate the minimum revenue for each month by year
SELECT year,month,
MIN(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH       TOALL 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          28888
2021  02          28888

-- Calculate the minimum revenue from the beginning of the year to date for each month by year
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

**keep_clause**

KEEP aggregate function general syntax, please refer to [KEEP aggregate function](00Built-in Functions (yashan Mode).html#KeepAggregateFunction).

When using the KEEP clause, the MIN function cannot use DISTINCT.

***Example***

```sql
-- The finance_info table records annual, monthly, and institutional revenue
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Calculate the minimum salary for the earliest year
SELECT MIN(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

MIN(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                              2888

-- Calculate the minimum salary for the latest year
SELECT MIN(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

MIN(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                            28888
```
