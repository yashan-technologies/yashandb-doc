```ebnf
dayofyear = DAYOFYEAR "(" expr ")" .
```

The DAYOFYEAR function is used to calculate the day of the year for the [expr](../General SQL Syntax/expr), returning an INT type value.

**expr**

The value of expr must be of TIMESTAMP type or another type that can be converted to TIMESTAMP type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT DAYOFYEAR('01-07-26 1:1:1.123') res;
         res
------------
         207 

SELECT DAYOFYEAR('2022-10-28 23:59:59.999999999') res;
         res
------------
         302
```
