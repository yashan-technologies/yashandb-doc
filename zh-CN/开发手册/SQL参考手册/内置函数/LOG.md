```ebnf
log = LOG "(" expr1 "," expr2 ")".
```

LOG函数计算expr2以expr1为底的对数。

本函数的规则如下：

- expr1和expr2均为YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

- 根据对数的数学概念，expr1应该为除0和1以外的正数，expr2应该为任意一个正数，除此之外的其他情况函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    |  expr1值（行）<br/>expr2值（列）| 除1以外的正数 | 1| 0| 负数| Inf| -Inf| Nan| -Nan|
    | ----------- | ----- |-  | - | - | - | -| - | ---- |
    |    **正数**     |   对数    | Inf  |  0   | Nan  |  0   | Nan  | Nan  | Nan  |
    |    **1**     |    0    | Nan  |  0   | Nan  |  0   | Nan  | Nan  | Nan  |
    |      **0**      |   -Inf    | -Inf | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **负数**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |     **Inf**     |    Inf    | Inf  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **-Inf**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |     **Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
    |    **-Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    |  expr1值（行）<br/>expr2值（列） | 除1以外的正数 | 1| 0、负数、Nan、Inf、-Inf |
    | ------------ | ------ | - | - |
    |    **正数**     |   对数    | 0 | 函数返回错误 |
    |    **1**     |   0    | 函数返回错误 | 函数返回错误 |
    |    **0、负数、Nan、Inf、-Inf**     |   函数返回错误    | 函数返回错误 | 函数返回错误 |

- 当expr1或expr2的值为NULL时，函数返回NULL。

- 当expr1或expr2的值均为合法的非NULL值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr1或expr2的值不含FLOAT/DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

示例1

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

示例2

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
