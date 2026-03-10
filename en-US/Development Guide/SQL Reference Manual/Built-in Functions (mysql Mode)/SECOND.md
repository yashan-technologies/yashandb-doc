```ebnf+diagram
second::= SECOND "(" expr ")" 
```

The SECOND function is used to calculate the seconds information of [expr](../General SQL Syntax/expr) and returns an INT type value.

**expr**

The value of expr must be of TIME type or another type that can be converted to TIME type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SECOND('11:37:26') res;
         RES
------------
          26 

SELECT SECOND('2022-10-28 23:59:59.111') res FROM DUAL;
         RES
------------
          79
```
