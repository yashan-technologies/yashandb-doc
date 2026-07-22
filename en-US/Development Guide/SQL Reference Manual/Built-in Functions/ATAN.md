```ebnf
atan = ATAN  "(" expr ")" .
```

The ATAN function returns the arctangent value of the given parameter, where the parameter is an angle represented in radians, and its magnitude is unlimited (only constrained by the range specified by its data type). The function returns a value within the interval [-π/2,π/2].

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) must be numeric or a character type that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | ATAN(expr) |
    | ---- | -------- |
    | Nan  | Nan       |
    | Inf  | π/2       |
    | -Inf | -π/2      |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | ATAN(expr) |
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

SELECT ATAN(-1) res FROM DUAL;
        RES
-----------
 -7.85E-001

SELECT ATAN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ATAN(1) res FROM DUAL;
        RES
-----------
 7.854E-001

SELECT ATAN('inf') res FROM DUAL;
        RES
-----------
 1.571E+000
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT ATAN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ATAN('nan') res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT ATAN('inf') res FROM DUAL;
YAS-04426 the argument value is out of range
```
