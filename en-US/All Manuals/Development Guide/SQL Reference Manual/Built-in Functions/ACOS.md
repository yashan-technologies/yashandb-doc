```ebnf+diagram
acos::= ACOS  "(" expr ")" 
```

The ACOS function calculates the arc cosine of the given parameter, with the parameter expressed in radians, in the range of [-1, 1]. The function returns a DOUBLE type value in the range of [0, pi].

The value of [expr](../General SQL Syntax/expr) must be numeric or convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns a type not supported error.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT ACOS(-1) res FROM DUAL;
        RES
-----------
 3.142E+000

SELECT ACOS(0) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ACOS(1) res FROM DUAL;
        RES
-----------
          0
```
