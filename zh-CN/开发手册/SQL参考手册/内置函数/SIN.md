```ebnf
sin = SIN  "(" expr ")" .
```

SIN函数返回给定参数的正弦值，参数为以弧度表示的角度，大小本身无限制（只受限于其所属数据类型所规定范围）。

本函数的规则如下：

- [expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

- 当expr的值为NULL时，函数返回NULL。

- 当expr的值为Nan、Inf、-Inf时，函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    | expr | SIN(expr) |
    | ---- | -------- |
    | Nan、Inf、-Inf  | Nan       |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    | expr | SIN(expr) |
    | ---- | -------- |
    | Nan、Inf、-Inf | 函数返回错误 |

- 当expr为其他有效值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr的值不含FLOAT或DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

示例1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT SIN(30*3.1415926/180) res FROM DUAL;
                 RES
--------------------
            5.0E-001

SELECT SIN(90*3.1415926/180) res FROM DUAL;
                 RES
--------------------
            1.0E+000

SELECT SIN(45*3.1415926/180) res FROM DUAL;
                 RES
--------------------
          7.071E-001

SELECT SIN('nan') res FROM DUAL;
                 RES
--------------------
               Nan

SELECT SIN('inf') res FROM DUAL;
                 RES
--------------------
               Nan
```

示例2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

SELECT SIN(0) res FROM DUAL;
        RES
-----------
        0

SELECT SIN('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
