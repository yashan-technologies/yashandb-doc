```ebnf+diagram
dayofweek::= DAYOFWEEK "(" expr ")" 
```

The DAYOFWEEK function is used to calculate the day of the week for [expr](../General SQL Syntax/expr) (with Sunday as the first day), returning an INT type value.

**expr**

The value of expr must be of TIMESTAMP/DATE type or other types that can be converted to TIMESTAMP/DATE type.

* Whether the value of expr allows zero in the year, month, or day depends on whether NO_ZERO_DATE and NO_ZERO_IN_DATE are included in [SQL_MODE](../../../参考手册/系统变量（mysql模式）.html#sql_mode).
* When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT DAYOFWEEK(DATE '2022-7-26') res FROM DUAL;
         RES
------------
           3

-- When the number of microseconds exceeds 6 digits, it is rounded up.
SELECT DAYOFWEEK('2022-10-28 23:59:59.999999999') res FROM DUAL;
         RES
------------
           7

SELECT DAYOFWEEK('2022-10-28 23:59:59.99999901') res FROM DUAL;
         RES
------------
           6
```
