```ebnf
last_value = LAST_VALUE "(" expr [(RESPECT|IGNORE) NULLS] ")" OVER "(" analytic_clause ")".
analytic_clause = "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")".
```

LAST_VALUE is a window function that operates on HEAP tables and supports sliding windows. This function calculates the set of window data based on the specified window conditions and returns the value of [expr](../General SQL Syntax/expr) corresponding to the last row of that set. If the last value is NULL and IGNORE NULLS is not specified, the function returns NULL.

Window functions cannot be nested, so expr must be a general expression other than a window function, with a data type excluded from large object types, JSON, and UDT.

**(RESPECT|IGNORE) NULLS**

Specifies whether the null values in expr are included in the function's calculation. The default is RESPECT NULLS, meaning that if the last value in the set is NULL, the function will return NULL.

If IGNORE NULLS is specified, the function returns the last non-null value in the set. When all values in the set are NULL, NULL is returned. This setting is useful for data densification.

***Example*** for Heap tables

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

-- Returns the last EMPLOYEE_COUNT in each window ordered by AREA_NO, the default window when windowing_clause is omitted is UNBOUNDED PRECEDING to CURRENT ROW
SELECT LAST_VALUE(employee_count) OVER(order by AREA_NO) res FROM area1;
                 RES 
-------------------- 
                    
                 300
                 400
                 300
               

-- Returns the last non-null EMPLOYEE_COUNT in each window ordered by AREA_NO, the default window when windowing_clause is omitted is UNBOUNDED PRECEDING to CURRENT ROW
SELECT LAST_VALUE(employee_count IGNORE NULLS) OVER(order by AREA_NO) res FROM area1;
         RES 
------------ 
            
         300
         400
         300
         300   
```

**analytic_clause**

The general syntax for window functions.

In the windowing_clause statement, regardless of whether ROWS or RANGE is specified, LAST_VALUE will sort the parameter column to ensure stability in the returned value of the function under the specified RANGE (when the order_by_clause has the same rank, it is reasonable for the function to return any row's value under the same rank, resulting in instability of the returned value); however, this instability still exists under ROWS.

The sorted parameter columns are: expr in query_partition_clause, the sorting sequence in order_by_clause, and the parameter expr of the LAST_VALUE function.

***Example*** for Heap tables

```sql
-- The finance_info table records yearly, monthly, and institutional revenue
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Grouped by year, ranking the lowest revenue institution for each year
SELECT year,revenue_total,
LAST_VALUE(branch) OVER (PARTITION BY year ORDER BY revenue_total DESC) fr
FROM finance_info;
YEAR  REVENUE_TOTAL FR    
----- ------------- ----- 
2001           2888 0201 
2021          38888 0101 
2021          37778 0101 
2021          28888 0201 

-- The lowest revenue institution to date each month
SELECT year,month,revenue_total,
LAST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) fr
FROM finance_info;
YEAR  MONTH REVENUE_TOTAL FR    
----- ----- ------------- ----- 
2021  01            38888 0101 
2021  02            37778 0101 
2021  01            28888 0201 
2001  01             2888 0201    

-- The lowest revenue institution within three months for each month
SELECT year,month,revenue_total,
LAST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) fr
FROM finance_info;
YEAR  MONTH REVENUE_TOTAL FR    
----- ----- ------------- ----- 
2021  01            38888 0101 
2021  02            37778 0201 
2021  01            28888 0201 
2001  01             2888 0201  
```
