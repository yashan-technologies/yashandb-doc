```ebnf+diagram
stddev_pop::= STDDEV_POP "(" [ALL] expr ")"
```

The STDDEV_POP function calculates the population standard deviation of [expr](../General SQL Syntax/expr).

The return type is of double type. 

The value of expr should be numeric or a string that can be converted to NUMBER type. For other types, the function's return type is not supported.

In single-row calculations, the result of the STDDEV_POP function is 0.

In multi-row calculations, the system ignores rows with null values. If all rows are null, the result will be NULL.

Aggregate functions cannot be nested; therefore, expr must be a general expression other than aggregate functions.

**ALL**

This is the default value, indicating that no filtering is done on duplicate data in the expression input, and the population standard deviation is calculated directly.

***Example*** for Standalone Deployment Heap tables

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME          DHQ            EMPLOYEE_COUNT
------- ------------------ -------------- --------------
01      EastChina             Shanghai                       
02      WestChina             Chengdu                   300  
03      SouthChina             Guangzhou                 400  
04      NorthChina             Beijing                   300  
05      CentralChina             Wuhan           
 
-- Calculate the population standard deviation of employee count; empty rows will be ignored
SELECT STDDEV_POP(employee_count) res FROM area1;
RES
--------------------
47.1404521
```
