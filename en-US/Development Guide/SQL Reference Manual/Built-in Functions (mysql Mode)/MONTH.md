```ebnf+diagram
month::= MONTH "(" expr ")" 
```

The MONTH function is used to calculate the month information of [expr](../General SQL Syntax/expr) and returns an INT type value.

**expr**

The value of expr must be of TIMESTAMP type or another type that can be converted to TIMESTAMP type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT MONTH('2022-7-26') res;
         RES
------------
           7 

SELECT MONTH('2022-10-31 23:59:59.999999999') res FROM DUAL;
         RES
------------
          11
```
