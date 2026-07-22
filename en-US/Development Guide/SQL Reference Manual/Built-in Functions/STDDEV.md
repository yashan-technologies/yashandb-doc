```ebnf
stddev = STDDEV "(" [DISTINCT|ALL] expr ")" [keep_clause].

keep_clause = KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")".
```

The STDDEV function calculates the sample standard deviation of the value of [expr](../General SQL Syntax/expr).

When the input has only one row of data, the result of the STDDEV function is 0.

The return types of the STDDEV function are as follows:

*   When the value of expr is TINYINT, SMALLINT, INT, BIGINT, or NUMBER type, it returns a NUMBER type.
*   When the value of expr is FLOAT or DOUBLE type, it returns the same FLOAT or DOUBLE type as the parameter.
*   When the value of expr is NULL, it returns NULL.
*   When the value of expr is a literal, variable, or constant, it returns 0.
*   When the value of expr is of another type and cannot be converted to a NUMBER type, the calculation is not performed and a type conversion error is returned.

The value of expr should be numeric or convertible to NUMBER type character strings. Other types are not supported by the function return type.

In multi-row calculations, the system ignores rows with NULL values, and when all rows are NULL, the calculation result is NULL.

**DISTINCT**

Indicates that duplicate input data is filtered out before calculating the sample standard deviation.

**ALL**

The default value, which means that duplicate data in the input expression is not filtered, and the sample standard deviation is calculated directly.

***Example***

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME           DHQ             EMPLOYEE_COUNT 
------- ------------------- --------------- -------------- 
01      EastChina              Shanghai                         
02      WestChina              Chengdu                    300   
03      SouthChina              Guangzhou                  400   
04      NorthChina              Beijing                    300   
05      CentralChina              Wuhan                       
 
-- Calculate the sample standard deviation of employee count, empty rows will be ignored. This statement is equivalent to SELECT STDDEV(ALL employee_count) FROM area
SELECT STDDEV(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269    
 
-- When there is only one record, the sample standard deviation is 0
SELECT STDDEV(employee_count) res FROM area1 where area_no='02';
                 RES
--------------------
                   0

-- Calculate the sample standard deviation after removing duplicate employee counts
SELECT STDDEV(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
          70.7106781
```

**keep_clause**

The general syntax of the KEEP aggregate function, detailed information can be found in [KEEP aggregate function](00Built-in Functions.md#KeepAggregateFunction).

When using the KEEP clause, the STDDEV function cannot use DISTINCT.

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

-- Calculate the sample standard deviation of salaries for the earliest year
SELECT STDDEV(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

STDDEV(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
-----------------------------------------------------
                                                    0

-- Calculate the sample standard deviation of salaries for the latest year
SELECT STDDEV(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

STDDEV(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
----------------------------------------------------
                                           4605.1659
```
