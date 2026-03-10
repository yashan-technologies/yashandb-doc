```ebnf+diagram
lag::= LAG "("expr [(RESPECT|IGNORE) NULLS] "," offset ["," default] ")" OVER "(" [query_partition_clause] order_by_clause ")"
```

LAG is a [window function](00Built-in Functions.html#WindowFunction). This function provides access to the row located at a specified physical offset before the current cursor position based on a set of rows returned by the window query, and returns the value corresponding to [expr](../General SQL Syntax/expr) for that row.

Window functions cannot be nested; therefore, expr must be a generic expression other than window functions, and its data type can be any type other than large objects, JSON, and UDT.

**(RESPECT|IGNORE) NULLS**

Specifies whether NULL values in expr are included in the function's computation, defaulting to RESPECT NULLS.

**offset**

Specifies the expression for the physical offset. The offset must be a generic expression of the same type as expr, and it must be of a type that can be converted to NUMBER, excluding large objects and JSON, but it cannot be NULL.

- If offset is omitted, its default value is 1.

- If the offset exceeds the bounds of the table, the default value defined by default will be returned.

**default**

Specifies the default value returned by the function when no row record can be obtained by the given offset. Default must be a generic expression of the same type as expr, and its data type can be any type other than large objects, JSON, and UDT. It can be omitted, with NULL as the default value.

When default and expr are of different types, default will be converted to the type of expr, with the length taken as the maximum length of default and expr. If the conversion fails, an error will be reported.

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

-- Returns the EMPLOYEE_COUNT from the previous row sorted by AREA_NO
SELECT LAG(employee_count) OVER(ORDER BY AREA_NO) lag1 FROM area1;
               LAG1
-------------------
               
               
               300
               400
               300

-- Returns the previous non-null EMPLOYEE_COUNT sorted by AREA_NO
SELECT LAG(employee_count IGNORE NULLS) OVER(ORDER BY AREA_NO) lag2 FROM area1;
               LAG2
-------------------
               
               
               300
               400
               300

-- Returns the previous non-null EMPLOYEE_COUNT sorted by AREA_NO, returning 0 if null
SELECT LAG(employee_count IGNORE NULLS,1,0) OVER(ORDER BY AREA_NO) lag3 FROM area1;
               LAG3
-------------------
               0
               0
               300
               400
               300
```

**query_partition_clause**|**order_by_clause**

Generic syntax for window functions.

***Example***

```sql
-- The finance_info table records the income situation by year, month, and organization
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Statistics of each institution's revenue for each month compared to the previous month by year
SELECT year,month,revenue_total curr_month,
LAG(revenue_total,1,0) OVER (PARTITION BY year ORDER BY year,month,branch) last_month
FROM finance_info;
YEAR  MONTH  CURR_MONTH  LAST_MONTH 
----- ----- ----------- ----------- 
2001  01           2888           0
2021  01          38888           0
2021  01          28888       38888
2021  02          37778       28888 
```
