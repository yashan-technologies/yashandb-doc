```ebnf
sinh = SINH  "(" expr ")" .
```

The SINH function returns the hyperbolic sine value of its argument, which has no inherent size limitation (only constrained by the range defined by its data type).

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) can be numeric or a character type that can be converted to NUMBER (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | SINH(expr) |
    | ---- | -------- |
    | Nan  | Nan       |
    | Inf  | Inf       |
    | -Inf | -Inf      |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | SINH(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf | function returns error |

- When expr is other valid values, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr does not contain FLOAT or DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

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

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT SINH(1) res FROM DUAL;
               RES
------------------
1.1752011936438014

SELECT SINH('Nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
