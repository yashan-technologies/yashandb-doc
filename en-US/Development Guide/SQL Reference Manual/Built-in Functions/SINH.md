```ebnf
sinh = SINH  "(" expr ")" .
```

The SINH function returns the hyperbolic sine value of its argument, which has no inherent size limitation (only constrained by the range defined by its data type), and the function returns a DOUBLE type value.

The value of [expr](../General SQL Syntax/expr) can be numeric or a character type that can be converted to NUMBER (conversion failure returns Invalid number error). For other types, the function returns type not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT SINH(2) res FROM DUAL;
RES
-----------
3.627E+000

SELECT SINH('2') res FROM DUAL;
RES
-----------
3.627E+000

SELECT SINH(b'10') res FROM DUAL;
RES
-----------
3.627E+000
```
