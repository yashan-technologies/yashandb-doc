```ebnf
cot = COT  "(" expr ")" .
```

The COT function returns the cotangent value of the given parameter, which is an angle represented in radians. The size of the angle itself is unlimited (only restricted by the range dictated by its data type), and the function will return a DOUBLE type value.

The value of [expr](../General SQL Syntax/expr) must be a numeric type or a string type that can be converted to DOUBLE. For other types, the function does not support the return type.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Heap tables

```sql
-- Calculate the cotangent value of expr represented in radians
SELECT COT(0) res FROM DUAL;
        res
-----------
        Inf

SELECT COT(45*3.1415926/180) res FROM DUAL;
        res 
----------- 
   1.0E+000

SELECT COT(60*3.1415926/180) res FROM DUAL;
        res 
----------- 
 5.774E-001
```
