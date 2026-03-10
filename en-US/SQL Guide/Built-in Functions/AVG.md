```ebnf+diagram
avg::= AVG "(" [DISTINCT|ALL] expr ")" [keep_clause|OVER "(" analytic_clause ")"]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The AVG function calculates the average value of the given parameters. The return types are as follows:

|Parameter Type |Return Value |
| --- | --- |
| TINYINT, SMALLINT, INT, BIGINT, NUMBER | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | DOUBLE |
| CHAR | NUMBER |
| VARCHAR | NUMBER |
| NCHAR | NUMBER |
| NVARCHAR | NUMBER |

The value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) must be numeric or convertible to NUMBER type (failure to convert returns an Invalid number error). Other types are not supported by the function return type.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function ignores rows with NULL values for expr. If all rows are NULL, the calculation result is NULL.

This aggregate function cannot be nested with group_id, grouping, grouping_id, or UDF aggregate functions. The expr can be other aggregate functions or other general expressions, with a maximum of one level of nesting for aggregate functions.

**DISTINCT**

Indicates that duplicate rows are filtered out when calculating the average.

**ALL**

The default value, indicating that the average is calculated for all rows.

***Example***

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME            DHQ             EMPLOYEE_COUNT
------- -------------------- --------------- --------------
01      EastChina               Shanghai                      
02      WestChina               Chengdu                    300
03      SouthChina               Guangzhou                  400
04      NorthChina               Beijing                    300
05      CentralChina               Wuhan                           
  
-- Calculate the average number of employees, ignoring rows with NULL employee count
SELECT AVG(employee_count) res FROM area1;
                RES
-------------------
         333.333333
  
-- Calculate the average number of employees (filtering out duplicates), ignoring rows with NULL employee count
SELECT AVG(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
                 350
-- Nested aggregate
SELECT AVG(AVG(employee_count)) res FROM area1 GROUP BY employee_count;      
        RES
-----------
          0
```

**OVER**

When the OVER keyword is specified, AVG functions as a [window function](00Built-in Functions.html#WindowFunction) and supports sliding windows, returning the average values of multiple rows.

**analytic_clause**

General syntax for window functions, for details see [window function](00Built-in Functions.html#WindowFunction).

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

-- Annual statistics for average revenue of all institutions each month, and average revenue of all institutions from the beginning of the year to now
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

The general syntax for KEEP aggregate functions can be found in [KEEP aggregate function](00Built-in Functions.html#KeepAggregateFunction).

When using the KEEP clause, the AVG function cannot use DISTINCT.

The KEEP aggregate function applies only to HEAP tables.

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

-- Calculate the average salary for the earliest year
SELECT AVG(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

AVG(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
--------------------------------------------------
                                              2888

-- Calculate the average salary for the latest year
SELECT AVG(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

AVG(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
-------------------------------------------------
                                       35184.6667
```
