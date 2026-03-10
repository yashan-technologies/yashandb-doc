```ebnf+diagram
count::= COUNT "(" ("*"|([DISTINCT|ALL] expr)) ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The COUNT function counts the number of record rows based on the given parameter [expr](../General SQL Syntax/expr). This function returns a BIGINT value and will never return NULL.

In single-row calculations, when the value of expr is NULL, the function returns 0.

In multi-row calculations, when expr is a literal, variable, or constant, the function counts all rows, including empty rows. Otherwise, the function ignores rows where the expr value is empty. When all rows are empty, the calculation result is 0.

This aggregate function cannot be nested with group_id, grouping, grouping_id, or UDF aggregate functions. The expr can be other aggregate functions or general expressions; the nesting depth of aggregate functions cannot exceed one layer, and its types can be any data type other than UDT.

**\***

Indicates that all rows are counted, including empty rows.

**DISTINCT**

Indicates that the expression results will be filtered for duplicates, counting all unique non-empty rows.

**ALL**

The default value, indicating that the expression results are not filtered for duplicates and counts all non-empty rows.

***Example***

```sql
-- Count the number of rows in the branches table
SELECT COUNT(1) res FROM branches;
                  RES
---------------------
                   12
  
-- Count the number of non-empty rows for the area_no field in the branches table. This statement is consistent with SELECT COUNT(ALL area_no) FROM branches.
SELECT COUNT(area_no) res FROM branches;
                  RES
---------------------
                   10
  
-- Count the number of unique non-empty rows for the area_no field in the branches table
SELECT COUNT(DISTINCT area_no) res FROM branches;
                  RES
---------------------
                    4

-- Nested aggregation
SELECT COUNT(COUNT(employee_count)) res FROM area1 GROUP BY employee_count;    
                  RES
---------------------
                    3
```

**OVER**

When the OVER keyword is specified, COUNT will act as a [window function](00Built-in Functions (yashan Mode).html#WindowFunction) and supports sliding windows, returning the count of multiple rows.

**analytic_clause**

General syntax for window functions.

***Example***

```sql
-- The finance_info table records revenue by year, month, and branch
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Count the number of institutions with revenue for each month by year
SELECT year,month,
COUNT(revenue_total) OVER (PARTITION BY year ORDER BY month) toall
FROM finance_info;
YEAR  MONTH                 TOALL
----- ----- --------------------- 
2001  01                        1
2021  01                        2
2021  01                        2
2021  02                        3

-- Count the number of institutions with revenue from the beginning of the year to the present for each month
SELECT year,month,
COUNT(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info;
YEAR  MONTH                 TONOW 
----- ----- --------------------- 
2001  01                        1
2021  01                        1
2021  01                        2
2021  02                        3
```

**keep_clause**

General syntax for KEEP aggregate functions. For more details, see [KEEP aggregate function](00Built-in Functions (yashan Mode).html#KeepAggregateFunction).

When using the KEEP clause, the COUNT function cannot use DISTINCT.

***Example***

```sql
-- The finance_info table records revenue by year, month, and branch
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Count the total revenue for the earliest year
SELECT COUNT(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

COUNT(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
----------------------------------------------------
                                                   6

-- Count the total revenue for the latest year
SELECT COUNT(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

COUNT(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
---------------------------------------------------
                                                 18
```
