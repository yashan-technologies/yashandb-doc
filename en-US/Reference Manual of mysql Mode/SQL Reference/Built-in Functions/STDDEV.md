```ebnf
stddev = STDDEV "(" [DISTINCT|ALL] expr ")".
```

The STDDEV function calculates the population standard deviation of the value of [expr](../General SQL Syntax/expr) and is a synonym for [STDDEV_POP](STDDEV_POP).

The return type is double.

The value of expr must be numerical or convertible to NUMBER type from a string type. Other types are not supported by the function's return type.

The calculation result of the STDDEV function is 0 in single-row computations.

In multi-row computations, the system ignores rows with null values, and if all rows are null, the result is NULL.

Aggregate functions cannot be nested; therefore, expr must be a general expression excluding aggregate functions.

**DISTINCT**

Indicates that duplicate input data is filtered out before calculating the sample standard deviation.

**ALL**

The default value, indicating that duplicate data in the expression input will not be filtered, and the overall standard deviation will be calculated directly.

***Example*** for Standalone Deployment Heap tables

```sql
-- The area1 table contains the following fields and data
SELECT area_no,area_name,DHQ,employee_count FROM area1;
area_no area_name          DHQ            employee_count
------- ------------------ -------------- --------------
01      EastChina             Shanghai                       
02      WestChina             Chengdu                   300  
03      SouthChina             Guangzhou                 400  
04      NorthChina             Beijing                   300  
05      CentralChina             Wuhan           
 
-- Calculate the population standard deviation of employee count, ignoring empty rows
SELECT STDDEV(employee_count) res FROM area1;
res
--------------------
47.1404521
```
