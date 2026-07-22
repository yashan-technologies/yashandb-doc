```ebnf
cot = COT  "(" expr ")" .
```

The COT function returns the cotangent value of the given parameter, where the parameter is an angle expressed in radians and its size is unrestricted (limited only by the range specified by its data type).

The rules of this function are as follows:

- The value of [expr](../General SQL Syntax/expr) should be numeric or a character type that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- When expr is NULL, the function returns NULL.

- When expr is Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | COT(expr) |
    | ---- | -------- |
    | Nan, Inf, -Inf  | Nan       |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | COT(expr) |
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

-- Calculate the cotangent value of the result of expr expressed in radians
SELECT COT(0) res FROM DUAL;
        RES
-----------
        Inf

SELECT COT(45*3.1415926/180) res FROM DUAL;
        RES
-----------
   1.0E+000

SELECT COT(60*3.1415926/180) res FROM DUAL;
        RES
-----------
 5.774E-001

SELECT COT('nan') res FROM DUAL;
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

SELECT COT(1) res FROM DUAL;
               RES
------------------
.64209261593433065

SELECT COT('nan') res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT COT(0) res FROM DUAL;
YAS-04426 the argument value is out of range
```
