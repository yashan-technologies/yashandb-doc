```ebnf+diagram
tan::= TAN  "(" expr ")" 
```

The TAN function returns the tangent value of the given parameter, where the parameter is the angle expressed in radians. The magnitude itself is unrestricted (only limited by the range defined by its data type), and the function returns a value of type DOUBLE.

The value of [expr](../General SQL Syntax/expr) can be numeric or a character type that can be converted to a NUMBER type (if the conversion fails, it returns an Invalid number error). For other types, the function returns type not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT TAN(0) res FROM DUAL;
        RES
-----------
          0

SELECT TAN(30*3.1415926/180) res FROM DUAL;
                 RES
--------------------
          5.774E-001

SELECT TAN(45*3.1415926/180) res FROM DUAL;
                 RES
--------------------
            1.0E+000
```
