```ebnf+diagram
minute::= MINUTE "(" expr ")" 
```

The MINUTE function is used to calculate the minute information of [expr](../General SQL Syntax/expr), returning an INT type value.

**expr**

The value of expr must be of TIME type or other types that can be converted to TIME type.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT MINUTE('10:17:26') res;
         res
------------
          17 

SELECT MINUTE('2022-10-28 23:59:59.111') res;
         res
------------
          59
```
