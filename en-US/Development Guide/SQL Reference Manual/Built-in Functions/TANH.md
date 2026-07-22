```ebnf
tanh = TANH  "(" expr ")" .
```

The TANH function returns the hyperbolic tangent of its argument, which is not limited in size (only constrained by the range defined for its data type). The function returns a DOUBLE type value.

The value of [expr](../General SQL Syntax/expr) can be a numeric type or a character type that can be converted to NUMBER (conversion failure returns an Invalid number error). For other types, the function returns a type not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT TANH(1) res FROM DUAL;
RES
-----------
7.616E-001

SELECT TANH('1') res FROM DUAL;
RES
-----------
7.616E-001

SELECT TANH(b'1') res FROM DUAL;
RES
-----------
7.616E-001
```
