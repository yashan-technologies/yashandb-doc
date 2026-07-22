```ebnf
max = MAX "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"].

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

The MAX function calculates the maximum value of the given parameter [expr](../General SQL Syntax/expr).

The input type and return type are consistent.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function ignores rows where the expr value is empty. When all rows are empty, the calculation result is NULL.

Aggregate functions cannot be nested, so expr should be a general expression that is not an aggregate function.

The BLOB and TEXT type are not allowed to calculate with this function.

**DISTINCT**

Indicates that duplicate rows will be filtered out when calculating the maximum value.

**ALL**

The default value, indicating that the maximum value will be calculated for all rows.

***Example*** for  Heap tables

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
area_no area_name              DHQ           employee_count
------- ---------------------- ------------- --------------
01      EastChina                 Shanghai                    
02      WestChina                 Chengdu                  300
03      SouthChina                 Guangzhou                400
04      NorthChina                 Beijing                  300
05      CentralChina                 Wuhan                       
  
-- Calculate the maximum number of employees, consistent with the query result of SELECT MAX(DISTINCT employee_count) FROM area1
SELECT MAX(employee_count) res FROM area1;
         res 
------------ 
         400

SELECT MAX(true) FROM sys.dual;

MAX(true) 
--------- 
        1
```

**OVER**

When the OVER keyword is specified, MAX will act as a [window function](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction), supporting sliding windows and returning the maximum value across multiple rows.

**analytic_clause**

The general syntax for window functions, for details please refer to [window functions](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction).

***Example*** for  Heap tables

```sql
-- The finance_info table records income by year, month, and branch
SELECT year,month,branch,revenue_total FROM finance_info;
year  month branch revenue_total 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Calculate the highest income for each month by year
SELECT year,month,
MAX(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
year  month       toall 
----- ----- ----------- 
2001  01           2888
2021  01          38888
2021  01          38888
2021  02          38888

-- Calculate the highest income from the beginning of the year to date for each month
SELECT year,month,
MAX(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
year  month       tonow 
----- ----- ----------- 
2001  01           2888
2021  01          28888
2021  01          38888
2021  02          38888
```
