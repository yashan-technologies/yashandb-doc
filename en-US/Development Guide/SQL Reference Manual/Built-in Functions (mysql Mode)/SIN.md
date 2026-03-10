```ebnf+diagram
sin::= SIN  "(" expr ")" 
```

The SIN function returns the sine value of the given parameter, where the parameter is an angle expressed in radians. The magnitude is unlimited (only restricted by the range defined by its data type). The function returns a DOUBLE type value that falls within the interval [-1,1].

The value of [expr](../General SQL Syntax/expr) can be numeric or a string that can be converted to DOUBLE type. For other types, the function will return a type unsupported error.

If the value of expr is NULL, the function will return NULL.

***Example*** for Standalone Deployment Heap tables

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
