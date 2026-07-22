```ebnf
ln = LN "(" expr ")".
```

LN函数用于计算[expr](../通用SQL语法/expr)的自然对数。

本函数的规则如下：

- expr的值为须为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

- 基于自然对数的数学概念，expr的值应该为一个正数，除此之外的其他情况函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    | expr | LN(expr) |
    | ---- | -------- |
    | 正数 | 自然对数 |
    | 0    | -Inf     |
    | 负数 | Nan      |
    | Inf  | Inf      |
    | -Inf | Nan      |
    | Nan  | Nan      |
    | -Nan | Nan      |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    | expr | LN(expr) |
    | ---- | -------- |
    | 正数 | 自然对数 |
    | 0、负数、Nan、Inf、-Inf | 函数返回错误 |
    
- 当expr的值为NULL时，函数返回NULL。

- 当expr的值为合法的非NULL值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr的值不含FLOAT或DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

示例1

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

示例2

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
