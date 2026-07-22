```ebnf
first_value = FIRST_VALUE "(" expr [(RESPECT|IGNORE) NULLS] ")" OVER "(" analytic_clause ")".
analytic_clause = "(" (query_partition_clause | order_by_clause) [windowing_clause] ")".
```

FIRST_VALUE is a [window function](00Built-in Functions.md#WindowFunction) and supports sliding windows. This function calculates the window data set based on the given window conditions and returns the value of [expr](../General SQL Syntax/expr) corresponding to the first row of that set. If the first value is NULL and IGNORE NULLS is not specified, the function returns NULL.

Window functions cannot be nested, so expr can be any general expression excluding window functions. The data type of expr can be any type other than CLOB, BLOB, NCLOB, JSON, UDT.

**(RESPECT|IGNORE) NULLS**

Specifies whether the null values of expr are included in the function's computation. The default is RESPECT NULLS, meaning if the first value in the set is NULL, the function will return NULL.

If IGNORE NULLS is specified, the function returns the first non-null value in the set. If all values in the set are null, it returns NULL. This setting is useful for data densification.

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

-- Returns the first EMPLOYEE_COUNT in each window ordered by AREA_NO. The default window when windowing_clause is omitted is UNBOUNDED PRECEDING to CURRENT ROW
SELECT FIRST_VALUE(employee_count) OVER(order by AREA_NO) res FROM area1;
                RES
-------------------
               
               
               
               
               

-- Returns the first non-null EMPLOYEE_COUNT in each window ordered by AREA_NO. The default window when windowing_clause is omitted is UNBOUNDED PRECEDING to CURRENT ROW
SELECT FIRST_VALUE(employee_count IGNORE NULLS) OVER(order by AREA_NO) res FROM area1;
                RES
-------------------
                
                300
                300
                300
                300        
```

**analytic_clause**

The general syntax for window functions.

In the windowing_clause statements, regardless of whether ROWS or RANGE is specified, FIRST_VALUE will sort the parameter column to ensure stability of return values in the specified RANGE (it is reasonable for the function to return any one of the values at the same rank when order_by_clause has the same rank, which may lead to instability of return values), but instability still exists in the case of ROWS.

The parameters for sorting are: expr in the query_partition_clause, the order sequence in the order_by_clause, and the parameter expr of the FIRST_VALUE function.

***Example*** for Standalone Deployment

```sql
-- The finance_info table records income situation by year, month, and branch
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Grouped by year, and rank the highest income branch in each year
SELECT year,revenue_total,
FIRST_VALUE(branch) OVER (PARTITION BY year ORDER BY revenue_total DESC) fr
FROM finance_info;
YEAR  REVENUE_TOTAL FR
----- ------------- -------------------- 
2001           2888 0201                
2021          38888 0101                
2021          37778 0101                
2021          28888 0101   

-- The highest income branch to date each month
SELECT year,month,revenue_total,
FIRST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) fr
FROM finance_info;
YEAR  REVENUE_TOTAL FR
----- ------------- -------------------- 
2021  01            38888 0101                
2021  02            37778 0101                
2021  01            28888 0101                
2001  01             2888 0101     

-- The highest income branch in the last three months each month
SELECT year,month,revenue_total,
FIRST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) fr
FROM finance_info;
YEAR  REVENUE_TOTAL FR
----- ------------- -------------------- 
2021  01            38888 0101                
2021  02            37778 0101                
2021  01            28888 0101                
2001  01             2888 0201   
```
