```ebnf
stddev_pop = STDDEV_POP "(" expr ")".
```

The STDDEV_POP function calculates the population standard deviation of the value of [expr](../General SQL Syntax/expr).

The return types of the STDDEV_POP function are as follows:

*   When the value of expr is TINYINT, SMALLINT, INT, BIGINT, or NUMBER type, the return type is NUMBER.
*   When the value of expr is FLOAT or DOUBLE type, the return type is the same as the parameter, FLOAT or DOUBLE.
*   When the value of expr is NULL, the return value is NULL.
*   When the value of expr is a literal, variable, or constant, the return value is 0.
*   When the value of expr is of another type that cannot be converted to NUMBER type, no calculation is performed, and a type conversion error is returned.

The value of expr must be numeric or convertible to NUMBER type characters. Other types are not supported for the function's return type.

In multi-row calculations, the system ignores rows with null values. If all rows are null, the result of the calculation is NULL.

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
 
-- Calculate the population standard deviation of employee count, empty rows will be ignored
SELECT STDDEV_POP(employee_count) res FROM area1;
                 RES
--------------------
          47.1404521
```
