```ebnf+diagram
asin::= ASIN  "(" expr ")" 
```

The ASIN function returns the arcsine of the given parameter, expressed in radians, with a size in the range of [-1,1]. The function will return a DOUBLE type value in the range of [-pi/2, pi/2].

The value of [expr](../General SQL Syntax/expr) must be numeric or convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT ASIN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ASIN(-1) res FROM DUAL;
        RES
-----------
 -1.57E+000

SELECT ASIN(1) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ASIN(0.5) res FROM DUAL;
        RES
-----------
 5.236E-001
```
