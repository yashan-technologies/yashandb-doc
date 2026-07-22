```ebnf
avg = AVG "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"].

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

The AVG function calculates the average value of the given parameter.

The mapping relationship between input parameter types and return types is as follows:

|Parameter Type |Return Value |
|-------------------------------------------------------------------------------------------------------|--------|
| TINYINT, SMALLINT, INT, BIGINT, NUMBER, BOOL, BIT, TINYINT UNSIGNED, SMALLINT UNSIGNED, INT UNSIGNED, BIGINT UNSIGNED | NUMBER |
| DOUBLE, FLOAT, VARCHAR, CHAR, NVARCHAR, NCHAR, BINARY                                                       | DOUBLE |

The value of [expr](../General SQL Syntax/expr) must be numeric or a character type that can be converted to NUMBER (conversion failures return Invalid number error). For other types, the function's return type is not supported.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the expr value is empty; if all rows are empty, the calculation result is NULL.

Aggregate functions cannot be nested, so expr must be a general expression other than aggregate functions.

**DISTINCT**

Indicates that duplicate rows will be filtered out when calculating the average.

**ALL**

The default value, indicating that the average is calculated for all rows.

***Example*** for Standalone Deployment Heap tables

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
area_no area_name            DHQ             employee_count
------- -------------------- --------------- --------------
01      EastChina               Shanghai                      
02      WestChina               Chengdu                    300
03      SouthChina               Guangzhou                  400
04      NorthChina               Beijing                    300
05      CentralChina               Wuhan                           
  
-- Calculate the average employee count; rows with empty employee count are ignored
SELECT AVG(employee_count) res FROM area1;
                res
-------------------
         333.333333
  
-- Calculate the average employee count (filtering out duplicate values); rows with empty employee count are ignored
SELECT AVG(DISTINCT employee_count) res FROM area1;
                 res
--------------------
                 350
```

**OVER**

When the OVER keyword is specified, AVG will act as a [window function](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction), supporting sliding windows and returning the average value for multiple rows.

**analytic_clause**

The general syntax for window functions, please refer to [window functions](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction).

***Example*** for Standalone Deployment Heap tables

```sql
-- The finance_info table records income by year, month, and branch
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

-- Annual statistics of the average income of all branches for each month, and the average income of all branches from the beginning of the year to now
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
