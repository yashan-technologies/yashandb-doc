```ebnf
sum = SUM "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"].

keep_clause = KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")".

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

The SUM function calculates the numerical sum of the given parameter [expr](../General SQL Syntax/expr), with the following return value types:

|expr Type |Return Value |
| --- | --- |
| TINYINT, SMALLINT | BIGINT |
| INT, BIGINT | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | DOUBLE |
| NUMBER | NUMBER |

When the parameter is of other types and cannot be cast to the above types, the calculation will not be performed, and a type conversion error or type unsupported message will be returned.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the expr value is NULL. When all rows are NULL, the calculation result will be NULL.

This aggregate function cannot be nested with group_id, grouping, grouping_id, and UDF aggregate functions. The expr can be other aggregate functions or general expressions, and the nesting level of aggregate functions cannot exceed one layer, with types limited to numeric or character types.

**DISTINCT**

Indicates that duplicate rows will be filtered out when calculating the numerical sum.

**ALL**

Default value, indicates that the numerical sum will be calculated for all rows.

***Example***

```sql
-- The branches1 table contains regional information, employee counts by area, etc.
  
-- The partition domain calculates the total number of employees, ignoring rows where employee counts for areas 01 and 04 are NULL. For area 02, there is only one row with a NULL value, thus the calculation returns NULL.
SELECT area_no,SUM(employee_count) sum FROM branches1 GROUP BY area_no;
AREA_NO                 SUM
------- -------------------
                         70
01                       70
04                       40
02                       
05                       40
  
-- The partition domain calculates the numerical sum of a constant.
SELECT area_no,SUM(10) sum FROM branches1 GROUP BY area_no;
AREA_NO         SUM
------- -----------
                 20
01               40
04               40
02               10
05               10

-- Nested aggregate
SELECT SUM(SUM(employee_count)) res FROM area1 GROUP BY employee_count;
        RES
-----------
       1000
```

**OVER**

When the OVER keyword is specified, SUM will function as a [window function](00Built-in Functions.md#WindowFunction), supporting sliding windows and returning the numerical sum for multiple rows.

**analytic\_clause**

General syntax for window functions.

***Example***

```sql
-- The finance_info table records income by year, month, and organization
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

-- Summarizing the total revenue for all organizations by month and year, as well as total revenue from the beginning of the year to date for all organizations.
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

General syntax for KEEP aggregate functions. For details, please refer to [KEEP aggregate function](00Built-in Functions.md#KeepAggregateFunction).

When using the KEEP clause, the SUM function cannot use DISTINCT.

***Example***

```sql
-- The finance_info table records income by year, month, and organization
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Summarizing the total salary for the earliest year
SELECT SUM(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

SUM(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                             17328

-- Summarizing the total salary for the latest year
SELECT SUM(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

SUM(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                           633324
```
