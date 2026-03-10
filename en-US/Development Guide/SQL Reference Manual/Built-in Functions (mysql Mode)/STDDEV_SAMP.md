```ebnf+diagram
stddev_samp::= STDDEV_SAMP "(" [ALL] expr ")"
```

The STDDEV_SAMP function calculates the sample standard deviation of the value of [expr](../General SQL Syntax/expr).

The return type is double.

The value of expr must be numeric or convertible to NUMBER type character. Other types are not supported by the function's return type.

In single-row calculations, the result of the STDDEV_SAMP function is 0.

In multi-row calculations, the system ignores rows with NULL values; when all rows are NULL, the result is NULL.

Aggregate functions cannot be nested, so expr must be a general expression other than aggregate functions.

**ALL**

This is the default value, indicating that duplicate data in the input expression is not filtered, and the sample standard deviation is calculated directly.

***Example*** for Standalone Deployment Heap tables

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
 
-- Calculate the sample standard deviation of employee count. Empty rows will be ignored; this statement is equivalent to SELECT STDDEV(ALL employee_count) FROM area.
SELECT STDDEV_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269    
 
-- When there is only one record, the sample standard deviation is 0.
SELECT STDDEV_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                   0
```
