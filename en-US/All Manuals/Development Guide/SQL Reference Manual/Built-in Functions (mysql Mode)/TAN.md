```ebnf+diagram
tan::= TAN  "(" expr ")" 
```

The TAN function returns the tangent value of the given parameter, which is an angle expressed in radians. The size of the angle itself is unlimited (limited only by the range specified by its data type), and the function returns a DOUBLE type data.

The value of [expr](../General SQL Syntax/expr) can be a numeric type or a character type that can be converted to DOUBLE. For other types, the function does not support the return type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

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
