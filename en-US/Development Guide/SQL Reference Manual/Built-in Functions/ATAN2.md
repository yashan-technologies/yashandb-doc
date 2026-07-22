```ebnf
atan2 = ATAN2  "(" expr1 "," expr2 ")" .
```

The ATAN2 function returns the arctangent value of the given parameters expr1/expr2, with the parameters expressed in radians. The magnitude itself is unlimited (only restricted by the range specified for its data type), and the function will return a value within the range [-π,π].

The rules of this function are as follows:

- The values of [expr](../General SQL Syntax/expr)1 and [expr](../General SQL Syntax/expr)2 must both be numeric types or character types that can be converted to NUMBER (a conversion failure will return an Invalid number error). For other types, the function does not support return types.

- If either expr1 or expr2 has a value of NULL, the function returns NULL.

- When expr1 or expr2 is 0, Nan, Inf, or -Inf, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr1 value (row)<br/>expr2 value (column) | valid value | Nan | Inf | -Inf |
    | ---- | ---- | -------- | -------- | -------- |
    | **valid value** | arctangent value | Nan | π/2 | -π/2 |
    | **Nan** | Nan | Nan | Nan | Nan |
    | **Inf** | π | Nan | π | π |
    | **-Inf** | -π | Nan | -π | -π |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr1 value (row)<br/>expr2 value (column) | valid value | Nan, Inf, -Inf |
    | ---- | ---- | -------- |
    | **valid value** | arctangent value | function returns error |
    | **0, Nan, Inf, -Inf** | function returns error | function returns error |

- When expr1 and expr2 are both other valid values, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr1 and expr2 do not contain FLOAT/DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT ATAN2(1,1) res FROM DUAL;
        RES
-----------
 7.854E-001

SELECT ATAN2(1,2) res FROM DUAL;
        RES
-----------
 4.636E-001

SELECT ATAN2(2,2) res FROM DUAL;
        RES
-----------
 7.854E-001
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT ATAN2(1,1) res FROM DUAL;
    	       RES
------------------
.78539816339744828

SELECT ATAN2('Nan',0) res FROM DUAL;
YAS-04426 the argument value is out of range
```
