```ebnf
second = SECOND "(" expr ")" .
```

The SECOND function is used to calculate the seconds information of [expr](../General SQL Syntax/expr) and returns an INT type value.

**expr**

The value of expr must be of TIME type or another type that can be converted to TIME type.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SECOND('11:37:26') res;
         res
------------
          26 

SELECT SECOND('2022-10-28 23:59:59.111') res FROM DUAL;
         res
------------
          59
```
