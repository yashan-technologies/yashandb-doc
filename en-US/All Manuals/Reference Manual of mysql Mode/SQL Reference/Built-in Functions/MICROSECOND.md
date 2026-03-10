```ebnf+diagram
microsecond::= MICROSECOND "(" expr ")" 
```

The MICROSECOND function is used to calculate the microsecond component of [expr](../General SQL Syntax/expr) and returns a value of type BIGINT.

**expr**

The value of expr must be of TIME type or another type that can be converted to TIME type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT MICROSECOND('2022-10-28 23:59:59.999999999') res FROM DUAL;
                  res
---------------------
                    0

SELECT MICROSECOND('2022-10-28 23:59:59.999999') res FROM DUAL;
                  res
---------------------
               999999
               
```
