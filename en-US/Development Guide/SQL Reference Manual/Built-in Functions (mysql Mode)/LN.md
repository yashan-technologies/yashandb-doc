```ebnf+diagram
ln::= LN "(" expr ")"
```

The LN function is used to calculate the natural logarithm of [expr](../General SQL Syntax/expr) and returns a value of type DOUBLE.

The value of expr must be a numeric type or a character type that can be converted to DOUBLE. For other types, the function does not support return types.

When the value of expr is NULL, the function returns NULL.

Based on the mathematical concept of the natural logarithm, the value of expr should be a positive number. The following table lists the return rules of the function for non-positive numbers and some special values:

| expr      | LN(expr)                       |
|-----------|--------------------------------|
| Negative numbers, -Inf, 0 | Invalid argument for logarithm |
| Inf       | Inf                            |
| Nan, -Nan  | Nan                            |

***Example*** for Standalone Deployment Heap tables

```sql
SELECT LN(2) res;
       RES
-----------
6.931E-001
```
