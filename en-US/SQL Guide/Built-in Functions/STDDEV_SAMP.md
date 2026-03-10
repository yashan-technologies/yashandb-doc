```ebnf+diagram
stddev_samp::= STDDEV_SAMP "(" expr ")"
```

The STDDEV_SAMP function calculates the sample standard deviation of the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr).

When the given parameter contains only one row of data, the result of the STDDEV_SAMP function is NULL.

The return value type of the STDDEV_SAMP function can be one of the following cases:

*   When the value of expr is of TINYINT, SMALLINT, INT, BIGINT, or NUMBER type, it returns a NUMBER type.
*   When the value of expr is of FLOAT or DOUBLE type, it returns the same FLOAT or DOUBLE type as the parameter.
*   When the value of expr is NULL, it returns NULL.
*   When the value of expr is a literal, variable, or constant, it returns 0.
*   When the value of expr is of another type that cannot be converted to NUMBER, the calculation is not performed, and a type conversion error is returned.

The value of expr should be a numeric type or a string type that can be converted to NUMBER. For other types, the function return type is not supported.

In multi-row calculations, the system ignores rows where the value is NULL, and when all rows are NULL, the calculation result is NULL.

***Example***

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
 
-- Calculate the sample standard deviation of employee count, rows with NULL will be ignored
 
SELECT STDDEV_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269
 
-- When there is only one record, the sample standard deviation is NULL
SELECT STDDEV_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                    
```
