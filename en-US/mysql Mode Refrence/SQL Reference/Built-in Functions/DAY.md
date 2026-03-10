```ebnf+diagram
day::= DAY "(" expr ")" 
dayofmonth::= DAYOFMONTH "(" expr ")" 
```

The DAY/DAYOFMONTH function is used to calculate the day of the month for the given [expr](../General SQL Syntax/expr), returning an INT type value.

**expr**

The value of expr must be of TIMESTAMP type or another type that can be converted to TIMESTAMP type.

When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT DAYOFMONTH('2022-7-26') res;
         res
------------
          26 

SELECT DAY('2022-10-28 23:59:59.999999999') res FROM DUAL;
         res
------------
          29

SELECT DAY('2022-10-28 23:59:59.99999901') res FROM DUAL;
         res
------------
          28
```
