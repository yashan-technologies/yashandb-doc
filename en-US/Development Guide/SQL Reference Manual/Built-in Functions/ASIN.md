```ebnf
asin = ASIN  "(" expr ")" .
```

The ASIN function returns the arcsine of the given parameter, expressed in radians, with a size in the range of [-1,1]. The function will return a value in the range of [-π/2,π/2].

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) must be numeric or convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, -Inf, or values outside the range [-1,1], the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | ASIN(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf  | Nan       |
    | outside [-1,1] | Nan |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | ASIN(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf | function returns error |
    | outside [-1,1] | function returns error |

- When expr is other valid values, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr does not contain FLOAT or DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT ASIN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ASIN(-1) res FROM DUAL;
        RES
-----------
 -1.57E+000

SELECT ASIN(1) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ASIN(0.5) res FROM DUAL;
        RES
-----------
 5.236E-001

SELECT ASIN(2) res FROM DUAL;
        RES
-----------
       Nan
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT ASIN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ASIN(2) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT ASIN('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
