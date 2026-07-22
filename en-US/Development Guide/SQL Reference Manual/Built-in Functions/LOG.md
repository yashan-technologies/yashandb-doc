```ebnf
log = LOG "(" expr1 "," expr2 ")".
```

The LOG function calculates the logarithm of expr2 with base expr1.

The rules of this function are as follows:

- expr1 and expr2 must be general expressions recognized by YashanDB, and their values must be numeric or can be converted to NUMBER type (conversion failure returns "Invalid number" error). For other types, the function returns "type not supported".

- According to the mathematical concept of logarithm, expr1 should be a positive number other than 0 and 1, and expr2 should be any positive number. For other cases, the function handling rules are affected by the configuration parameter [MATH_FUNC_RETURN_DECIMAL](../../../Reference Manual/Configuration Parameters.md#MFRD):

  - If MATH_FUNC_RETURN_DECIMAL = FALSE (default value), the handling rules are as follows:

    |  expr1 value (row)<br/>expr2 value (column) | positive number other than 1 | 1| 0| negative number| Inf| -Inf| Nan| -Nan|
    | ----------- | ----- | - | - | - | - | - | - | ---- |
    |    **positive number**     |   logarithm    | Inf  |  0   | Nan  |  0   | Nan  | Nan  | Nan  |
    |    **1**     |    0    | Nan  |  0   | Nan  |  0   | Nan  | Nan  | Nan  |
    |      **0**      |   -Inf    | -Inf | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **negative number**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |     **Inf**     |    Inf    | Inf  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **-Inf**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |     **Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **-Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |

  - If MATH_FUNC_RETURN_DECIMAL = TRUE, the handling rules are as follows:

    |  expr1 value (row)<br/>expr2 value (column) | positive number other than 1 | 1| 0, negative number, Nan, Inf, -Inf |
    | ------------ | ------ | - | - |
    |    **positive number**     |   logarithm    | 0 | function returns error |
    |    **1**     |   0    | function returns error | function returns error |
    |    **0, negative number, Nan, Inf, -Inf**     |   function returns error    | function returns error | function returns error |

- When expr1 or expr2 is NULL, the function returns NULL.

- When both expr1 and expr2 are valid non-NULL values, the return type of the function is also affected by the configuration parameter MATH_FUNC_RETURN_DECIMAL:

  - If MATH_FUNC_RETURN_DECIMAL = TRUE and expr1 or expr2 does not contain FLOAT/DOUBLE type, the function returns NUMBER type.

  - Otherwise, the function returns DOUBLE type.

***Example*** 1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT LOG(2,4) res FROM DUAL;
        RES
-----------
   2.0E+000

SELECT LOG(b'10',b'100') res FROM DUAL;
        RES
-----------
   2.0E+000

SELECT LOG(1,4) res FROM DUAL;
        RES
-----------
        Inf

SELECT LOG(2,0) res FROM DUAL;
        RES
-----------
       -Inf

SELECT LOG(2,'nan') res FROM DUAL;
        RES
-----------
       Nan

SELECT LOG('inf',4) res FROM DUAL;
        RES
-----------
       Nan

DROP TABLE IF EXISTS number_fd;
CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES(0,5.55);
INSERT INTO number_fd VALUES(1,5.55);
INSERT INTO number_fd VALUES(2,-5.55);
INSERT INTO number_fd VALUES(2,0);
INSERT INTO number_fd VALUES('2','Inf');
INSERT INTO number_fd VALUES('2','-Inf');
INSERT INTO number_fd VALUES('2','Nan');
INSERT INTO number_fd VALUES('2','-Nan');

SELECT LOG(numberf,numberd) res1, LOG(numberd,numberf) res2 FROM number_fd;
       RES1        RES2
----------- -----------
          0        -Inf
        Inf           0
        Nan         Nan
       -Inf           0
        Inf           0
        Nan         Nan
        Nan         Nan
        Nan         Nan
```

***Example*** 2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT LOG(2,4) res FROM DUAL;
        RES
-----------
          2

SELECT LOG(b'10',b'100') res FROM DUAL;
        RES
-----------
          2

SELECT LOG(2,1) res FROM DUAL;
        RES
-----------
          0

SELECT LOG(1,4) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT LOG(2,0) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT LOG(2,'nan') res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT LOG('inf',4) res FROM DUAL;
YAS-04426 the argument value is out of range
```
