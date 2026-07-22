```ebnf
sum = SUM "(" [DISTINCT|ALL] expr ")" [OVER "(" analytic_clause ")"].

analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

The SUM function calculates the sum of the given parameter [expr](../General SQL Syntax/expr), and its return value types are as follows:

|Parameter Type |Return Value |
|-------------------------------------------------|--------|
| TINYINT, SMALLINT, INT, BIGINT, NUMBER, BOOL, BIT, TINYINT UNSIGNED, SMALLINT UNSIGNED, MEDIUMINT UNSIGNED, INT UNSIGNED, BIGINT UNSIGNED     | NUMBER |
| DOUBLE, FLOAT, VARCHAR, CHAR, NVARCHAR, NCHAR, BINARY | DOUBLE |

When the parameter is of other types and cannot be converted to the above types, no calculation is performed, and a type conversion error or unsupported type is returned.

In single-row calculations, when the value of expr is NULL, the function returns NULL.

In multi-row calculations, the function ignores rows where the expr value is NULL. If all rows are NULL, the result is NULL.

Aggregate functions cannot be nested, thus expr must be a general expression that is not an aggregate function.

**DISTINCT**

Indicates that duplicate rows are filtered out during the sum calculation.

**ALL**

The default value, which indicates that the sum is calculated for all rows.

***Example*** for Standalone Deployment Heap tables

```sql
-- The branches1 table contains regional information, the number of employees in each region, etc.
  
-- The partition domain calculates the total number of employees. Rows corresponding to regions 01 and 04 with NULL employee counts are ignored in the calculation. For region 02, there is only one row that is NULL, so the calculation returns NULL.
SELECT area_no,SUM(employee_count) sum FROM branches1 GROUP BY area_no;
area_no                 sum
------- -------------------
                         70
01                       70
04                       40
02                       
05                       40
  
-- The partition domain calculates a constant sum.
SELECT area_no,SUM(10) sum FROM branches1 GROUP BY area_no;
area_no         sum
------- -----------
                 20
01               40
04               40
02               10
05               10
```

**OVER**

When the OVER keyword is specified, SUM acts as a [window function](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction), supporting a sliding window and returning the sum of multiple rows.

**analytic_clause**

The general syntax for window functions; for more details, please refer to [window function](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions.md#WindowFunction).

***Example*** for Standalone Deployment Heap tables

```sql
-- The finance_info table records the income situation by year, month, and institution.
SELECT year,month,branch,revenue_total FROM finance_info;
year  month branch revenue_total 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

SELECT year,month,
revenue_total curr,
SUM(revenue_total) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) curr
FROM finance_info;
year  month        curr       tonow 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          28888       28888
2021  01          38888       67776
2021  02          37778      105554

-- Yearly statistics of the total income for all institutions per month, and from the beginning of the year to now for all institutions.
SELECT year,month,
SUM(revenue_total) curr,
SUM(SUM(revenue_total)) OVER (PARTITION BY year ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) tonow
FROM finance_info
GROUP BY year,month;
year  month        curr       tonow 
----- ----- ----------- ----------- 
2001  01           2888        2888
2021  01          67776       67776
2021  02          37778      105554
```
