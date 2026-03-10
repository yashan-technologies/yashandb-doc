```ebnf+diagram
sign::= SIGN "(" expr ")"
```

The SIGN function returns the sign of the value represented by [expr](../General SQL Syntax/expr), returning 1 for positive integers, -1 for negative integers, and 0 for zero. The return value is of BIGINT type.

**expr**

[General Expression](../General SQL Syntax/expr), whose value must be of numeric type or another type that can be converted to numeric.

- When the value of expr is of date type, it will be converted to specific year, month, and day numeric values for the sign calculation.

- When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT SIGN(7.11) sign1,
       SIGN(-10.97) sign2,
       SIGN(0) sign3
FROM DUAL;
sign1                 sign2                 sign3 
--------------------- --------------------- --------------------- 
                    1                    -1                     0
```
