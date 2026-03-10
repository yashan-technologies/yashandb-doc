```ebnf+diagram
hour::= HOUR "(" expr ")" 
```

The HOUR function is used to calculate the hour information of [expr](../General SQL Syntax/expr), returning an INT type value.

**expr**

The value of expr must be of TIME type or another type that can be converted to TIME type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT HOUR('01:07:26.123') res;
         RES
------------
           1 

SELECT HOUR('2022-10-28 23:59:59.999999999') res;
         RES
------------
           24
```
