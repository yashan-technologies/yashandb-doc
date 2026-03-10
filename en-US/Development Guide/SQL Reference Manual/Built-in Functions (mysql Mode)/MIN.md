```ebnf+diagram
min::= MIN "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"]

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The MIN function calculates the minimum value of the given parameter [expr](../General SQL Syntax/expr).

The input type and return type are the same.

In single-row calculations, if the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the value of expr is NULL; if all rows are NULL, the result will be NULL.

Aggregate functions cannot be nested, so expr must be a generic expression other than aggregate functions, and its data type can be any type except boolean, large objects, JSON, and UDT.

**DISTINCT**

Indicates that duplicate rows will be filtered out when calculating the minimum value.

**ALL**

The default value, indicating that the minimum value will be calculated for all rows.

***Example*** for Standalone Deployment Heap tables

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
  
-- Calculate the minimum employee count, consistent with the result of the query SELECT MIN(DISTINCT employee_count) FROM area1
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

When the OVER keyword is specified, MIN will act as a [window function](../Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode).html#WindowFunction) and supports sliding windows, returning the minimum value across multiple rows.

**analytic_clause**

The general syntax for window functions; please refer to [window function](../Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode).html#WindowFunction) for more details.

***Example*** for Standalone Deployment Heap tables

```sql
-- The finance_info table records annual, monthly, and institutional revenue
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Calculate the lowest revenue for each month by year
SELECT year,month,
    MIN(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH       TOALL 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          28888
2021  02          28888

-- Calculate the lowest revenue from the beginning of the year to date for each month by year
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
