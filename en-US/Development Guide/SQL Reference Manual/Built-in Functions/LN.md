```ebnf
ln = LN "(" expr ")".
```

The LN function is used to compute the natural logarithm of [expr](../General SQL Syntax/expr).

The rules of this function are as follows:

- The value of expr must be numeric or can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

- Based on the mathematical concept of natural logarithm, the value of expr should be a positive number. For other cases, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    | expr | LN(expr) |
    | ---- | -------- |
    | positive number | natural logarithm |
    | 0    | -Inf     |
    | negative number | Nan      |
    | Inf  | Inf      |
    | -Inf | Nan      |
    | Nan  | Nan      |
    | -Nan | Nan      |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    | expr | LN(expr) |
    | ---- | -------- |
    | positive number | natural logarithm |
    | 0, negative number, Nan, Inf, -Inf | function returns error |

- When expr is NULL, the function returns NULL.

- When expr is a valid non-NULL value, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr does not contain FLOAT or DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT LN(2) res FROM DUAL;
        RES
-----------
   6.931E-001

SELECT LN('nan') res FROM DUAL;
        RES
-----------
       Nan

SELECT LN('inf') res FROM DUAL;
        RES
-----------
       Inf

CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES(0,-5.55);
INSERT INTO number_fd VALUES('Inf','-Inf');
INSERT INTO number_fd VALUES('Nan','-Nan');

SELECT LN(numberf) res1,LN(numberd) res2 FROM number_fd;
       RES1        RES2
----------- -----------
       -Inf         Nan
        Inf         Nan
        Nan         Nan
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT LN(2) res FROM DUAL;
               RES
------------------
.69314718055994529

SELECT LN(0) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT LN(-1) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT LN('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
