```ebnf+diagram
tan::= TAN  "(" expr ")" 
```

The TAN function returns the tangent value of the given parameter, which is an angle expressed in radians. The size of the angle itself is unlimited (limited only by the range specified by its data type), and the function returns a DOUBLE type data.

The value of [expr](../General SQL Syntax/expr) can be a numeric type or a character type that can be converted to DOUBLE. For other types, the function does not support the return type.

When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

yasql and MySQL Client handle the display printing mechanism of time formats differently, but this does not affect the use of business applications.

The following are query examples via yasql.

```sql
SELECT TAN(0) res FROM DUAL;
        res
-----------
          0

SELECT TAN(30*3.1415926/180) res FROM DUAL;
                 res
--------------------
          5.774E-001

SELECT TAN(45*3.1415926/180) res FROM DUAL;
                 res
--------------------
            1.0E+000
```

The following are query examples via MySQL Client.

```sql
SELECT TAN(30*3.1415926/180) res FROM DUAL;
+-------------------------+
| res                     |
+-------------------------+
| 5.7735025728078293E-001 |
+-------------------------+

SELECT TAN(0) res FROM DUAL;
+------+
| res  |
+------+
|    0 |
+------+

SELECT TAN(45*3.1415926/180) res FROM DUAL;
+-------------------------+
| res                     |
+-------------------------+
| 9.9999997320510381E-001 |
+-------------------------+
```