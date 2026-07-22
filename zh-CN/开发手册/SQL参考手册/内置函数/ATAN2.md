```ebnf
atan2 = ATAN2  "(" expr1 "," expr2 ")" .
```

ATAN2函数返回给定参数expr1/expr2的结果的反正切值，参数以弧度表示，大小本身无限制（只受限于其所属数据类型所规定范围），函数将返回一个大小在区间[-π,π]的数值。

本函数的规则如下：

- [expr](../通用SQL语法/expr)1和[expr](../通用SQL语法/expr)2的值均为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

- 当expr1或expr2中任一值为NULL时，函数返回NULL。

- 当expr1或expr2的值为0Nan、Inf、-Inf或0时，函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    | expr1值（行）<br/>expr2值（列） | 有效值 | Nan | Inf | -Inf |
    | ---- | ---- | -------- | -------- | -------- |
    | **有效值** | 反正切值 | Nan | π/2 | -π/2 |
    | **Nan** | Nan | Nan | Nan | Nan |
    | **Inf** | π | Nan | π | π |
    | **-Inf** | -π | Nan | -π | -π |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    | expr1值（行）<br/>expr2值（列） | 有效值 | Nan、Inf、-Inf |
    | ---- | ---- | -------- |
    | **有效值** | 反正切值 | 函数返回错误 |
    | **0、Nan、Inf、-Inf** | 函数返回错误 | 函数返回错误 |

- 当expr1和expr2的值均为其他有效值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr1和expr2的值均不含FLOAT/DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

示例1

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

示例2

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
