```ebnf+diagram
atan2::= ATAN2  "(" expr1 "," expr2 ")" 
```

The ATAN2 function returns the arctangent value of the given parameters expr1/expr2, with the parameters expressed in radians. The magnitude itself is unlimited (only restricted by the range specified for its data type), and the function will return a DOUBLE type value within the range [-pi, pi].

The values of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)1 and [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)2 must both be numeric types or character types that can be converted to NUMBER (a conversion failure will return an Invalid number error). For other types, the function does not support return types.

If either expr1 or expr2 has a value of NULL, the function returns NULL.

***Example***

```sql
SELECT ATAN2(1,1) res FROM DUAL;
        RES
-----------
 7.854E-001

SELECT ATAN2(1,2) res FROM DUAL;
        RES
-----------
 4.636E-001

SELECT ATAN2(2,2) res FROM DUAL;
        RES
-----------
 7.854E-001
```
