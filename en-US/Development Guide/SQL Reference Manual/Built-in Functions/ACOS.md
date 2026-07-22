```ebnf
acos = ACOS  "(" expr ")" .
```

The ACOS function calculates the arc cosine of the given parameter, with the parameter expressed in radians, in the range of [-1, 1]. The function returns a value in the range of [0,π].

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) must be numeric or convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, -Inf, or values outside the range [-1,1], the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | ACOS(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf  | Nan       |
    | outside [-1,1] | Nan |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | ACOS(expr) |
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

SELECT ACOS(-1) res FROM DUAL;
        RES
-----------
 3.142E+000

SELECT ACOS(0) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ACOS(1) res FROM DUAL;
        RES
-----------
          0

SELECT ACOS(2) res FROM DUAL;
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

set num 128

SELECT ACOS(-1) res FROM DUAL;
                                    RES
---------------------------------------
3.1415926535897932384626433832795028842

SELECT ACOS(0) res FROM DUAL;
                                  RES
--------------------------------------
                    1.5707963267948966

SELECT ACOS(2) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT ACOS('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
