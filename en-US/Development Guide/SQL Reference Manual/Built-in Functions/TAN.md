```ebnf
tan = TAN  "(" expr ")" .
```

The TAN function returns the tangent value of the given parameter, where the parameter is the angle expressed in radians. The magnitude itself is unrestricted (only limited by the range defined by its data type).

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) can be numeric or a character type that can be converted to a NUMBER type (if the conversion fails, it returns an Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | TAN(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf  | Nan       |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | TAN(expr) |
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

SELECT TAN('nan') res FROM DUAL;
                 RES
--------------------
               Nan
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT TAN(0) res FROM DUAL;
        RES
-----------
        0

SELECT TAN('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
