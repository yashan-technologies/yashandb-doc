```ebnf+diagram
atan::= ATAN  "(" expr ")" 
```

The ATAN function returns the arctangent value of the given parameter, where the parameter is an angle represented in radians, and its magnitude is unlimited (only constrained by the range specified by its data type). The function returns a DOUBLE type value within the interval [-pi/2, pi/2].

The value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) must be numeric or a character type that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns that the type is not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT ATAN(-1) res FROM DUAL;
        RES
-----------
 -7.85E-001

SELECT ATAN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ATAN(1) res FROM DUAL;
        RES
-----------
 7.854E-001
```
