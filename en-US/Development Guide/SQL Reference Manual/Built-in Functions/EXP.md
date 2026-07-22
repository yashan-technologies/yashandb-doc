```ebnf
exp = EXP "(" expr ")".
```

The EXP function calculates the mathematical result of the number represented by [expr](../General SQL Syntax/expr) with a base of e=2.71828183...

The rules of this function are as follows:

- The value of expr must be numeric and can be a numeric string; for other types, the function returns type not supported error.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | EXP(expr) |
    | ---- | -------- |
    | Nan  | Nan       |
    | Inf  | Inf       |
    | -Inf | 0         |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | EXP(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf | function returns error |

- When expr is other valid values, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr does not contain FLOAT or DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

- Due to display precision differences, the calculation result of this function can remain consistent with the calculation result of the same function in Oracle for the first 15 digits, after which discrepancies may occur.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

set numwidth 30
SELECT EXP(4.444444) res FROM DUAL;
                            RES
-------------------------------
        8.5152519871979379E+001

SELECT EXP(b'0101') res FROM DUAL;
                            RES
-------------------------------
         1.484131591025766E+002

SELECT EXP('Nan') res FROM DUAL;
                            RES
-------------------------------
                               Nan

SELECT EXP('Inf') res FROM DUAL;
                            RES
-------------------------------
                               Inf

SELECT EXP('-Inf') res FROM DUAL;
                            RES
-------------------------------
                               0
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT EXP(2) res FROM DUAL;
               RES
------------------
7.3890560989306504

SELECT EXP('Nan') res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT EXP('Inf') res FROM DUAL;
YAS-04426 the argument value is out of range
```
