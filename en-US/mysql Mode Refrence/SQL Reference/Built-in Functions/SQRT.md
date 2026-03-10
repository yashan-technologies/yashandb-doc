```ebnf+diagram
sqrt::= SQRT "(" expr ")"
```

The SQRT function calculates the square root of the value represented by [expr](../General SQL Syntax/expr) and returns a value of type DOUBLE.

The value of expr must be numeric or a character type that can be converted to DOUBLE. For other types, the function does not support a return type.

When the value of expr is negative, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SQRT(4);
              SQRT(4) 
--------------------- 
             2.0E+000
```
