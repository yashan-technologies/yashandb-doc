```ebnf
asin = ASIN  "(" expr ")" .
```

The ASIN function returns the arcsine of a given parameter, with the parameter expressed in radians, and within the range of [-1,1]. The function returns a DOUBLE type value within the range of [-pi/2,pi/2].

The value of [expr](../General SQL Syntax/expr) must be a numeric type or a character type that can be converted to DOUBLE. For other types, the function does not support the return type.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ASIN(0) res FROM DUAL;
        res
-----------
          0

SELECT ASIN(-1) res FROM DUAL;
        res
-----------
 -1.57E+000

SELECT ASIN(1) res FROM DUAL;
        res
-----------
 1.571E+000

SELECT ASIN(0.5) res FROM DUAL;
        res
-----------
 5.236E-001

```
