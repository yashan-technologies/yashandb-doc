```ebnf
exp = EXP "(" expr ")".
```

EXP函数计算以e=2.71828183...为底，[expr](../通用SQL语法/expr)表示的数值为指数的数学结果。

本函数的规则如下：

- expr的值须为数值型，可以是数值型字符串，对于其他类型，函数返回类型不支持错误。

- 当expr为NULL时，函数返回NULL。

- 当expr的值为Nan、Inf、-Inf时，函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    | expr | EXP(expr) |
    | ---- | -------- |
    | Nan  | Nan       |
    | Inf  | Inf       |
    | -Inf | 0         |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    | expr | EXP(expr) |
    | ---- | -------- |
    | Nan、Inf、-Inf | 函数返回错误 |

- 当expr为其他有效值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr的值不含FLOAT或DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

- 由于显示精度差异，本函数的计算结果与Oracle同函数的计算结果在前15位可以保持一致，之后可能会有差异。

示例1

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

示例2

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
