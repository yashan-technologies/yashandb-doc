```ebnf+diagram
lead::= LEAD "(" expr [(RESPECT|IGNORE) NULLS] "," offset "," default ")" OVER "(" [query_partition_clause] order_by_clause ")"
```

LEAD is a [window function](00Built-in Functions.html#WindowFunction). This function provides access to the rows that exceed the given physical offset from the position of the cursor in a set of rows returned by a window query and returns the value of [expr](../General SQL Syntax/expr) corresponding to that row record.

Window functions cannot be nested, so expr is a general expression other than window functions, and its data type can be any type excluding large objects, JSON, and UDT.

**(RESPECT|IGNORE) NULLS**

Specifies whether null values of expr are included in the function's calculation, defaulting to RESPECT NULLS.

**offset**

Specifies the expression for the physical offset, which must be a type convertible to NUMBER other than large objects and JSON.

The offset is a general expression that is the same as expr, but cannot be NULL.

If offset is omitted, its default value is 1.

If the offset exceeds the range of the table, the default value defined by default is returned.

**default**

Specifies the default value returned by the function when rows cannot be accessed using the given offset. Default is a general expression that is the same as expr, and its data type can be any type excluding large objects, JSON, and UDT. It can be omitted, in which case the default value is NULL.

When the type of default and expr is inconsistent, default will be converted to the type of expr, with the length being the maximum length of default and expr. If the conversion fails, an error is reported.

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

-- Returns EMPLOYEE_COUNT of the next row sorted by AREA_NO
SELECT LEAD(employee_count) OVER(ORDER BY AREA_NO) res FROM area1;
         RES 
------------ 
         300
         400
         300
               

-- Returns the next non-null EMPLOYEE_COUNT sorted by AREA_NO
SELECT LEAD(employee_count IGNORE NULLS) OVER(ORDER BY AREA_NO) res FROM area1;
         RES 
------------ 
         300
         400
         300
 

-- Returns the next non-null EMPLOYEE_COUNT sorted by AREA_NO, returns 0 if null
SELECT LEAD(employee_count IGNORE NULLS,1,0) OVER(ORDER BY AREA_NO) res FROM area1;
         RES 
------------ 
         300
         400
         300
           0
           0
```

**query_partition_clause**|**order_by_clause**

General syntax for window functions.

***Example***

```sql
-- The finance_info table records income data by year, month, and institution
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Yearly statistics for each institution's income comparison for the current month and the next month
SELECT year,month,revenue_total curr_month,
LEAD(revenue_total,1,0) OVER (PARTITION BY year ORDER BY year,month,branch) next_month
FROM finance_info;
YEAR  MONTH  CURR_MONTH  NEXT_MONTH 
----- ----- ----------- ----------- 
2001  01           2888           0
2021  01          38888       28888
2021  01          28888       37778
2021  02          37778           0
```
