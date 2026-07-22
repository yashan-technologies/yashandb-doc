```ebnf
sin = SIN  "(" expr ")" .
```

The SIN function returns the sine value of the given parameter, where the parameter is an angle expressed in radians, and its magnitude is unrestricted (limited only by the range defined by its data type). The function returns a DOUBLE type value in the range of [-1, 1].

The value of [expr](../General SQL Syntax/expr) can be numeric or a character type that can be converted to NUMBER type (a conversion failure results in an Invalid number error). For other types, the function returns a type not supported error.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT SIN(30*3.1415926/180) res FROM DUAL;
                 RES
--------------------
            5.0E-001

SELECT SIN(90*3.1415926/180) res FROM DUAL;
                 RES
--------------------
            1.0E+000

SELECT SIN(45*3.1415926/180) res FROM DUAL;
                 RES
--------------------
          7.071E-001
```
