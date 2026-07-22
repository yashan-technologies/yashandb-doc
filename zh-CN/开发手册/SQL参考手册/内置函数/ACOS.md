```ebnf
acos = ACOS  "(" expr ")" .
```

ACOS函数计算给定参数的反余弦值，参数为弧度表示，大小在区间[-1,1]，函数将返回一个大小在区间[0,π]的数值。

本函数的规则如下：

- [expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

- 当expr的值为NULL时，函数返回NULL。

- 当expr的值为Nan、Inf、-Inf或超出[-1,1]范围的值时，函数处理规则受配置参数[MATH_FUNC_RETURN_DECIMAL](../../../参考手册/配置参数.md#MFRD)影响：

  - 若MATH_FUNC_RETURN_DECIMAL = FALSE（默认值），处理规则如下：

    | expr | ACOS(expr) |
    | ---- | -------- |
    | Nan、Inf、-Inf  | Nan       |
    | 超出[-1,1] | Nan |

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE，处理规则如下：

    | expr | ACOS(expr) |
    | ---- | -------- |
    | Nan、Inf、-Inf | 函数返回错误 |
    | 超出[-1,1] | 函数返回错误 |

- 当expr为其他有效值时，函数返回值类型同样受配置参数MATH_FUNC_RETURN_DECIMAL影响：

  - 若MATH_FUNC_RETURN_DECIMAL = TRUE且expr的值不含FLOAT或DOUBLE类型，函数返回NUMBER类型。

  - 否则，函数返回DOUBLE类型。

示例1

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    FALSE

SELECT ACOS(-1) res FROM DUAL;
        RES
-----------
 3.142E+000

SELECT ACOS(0) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ACOS(1) res FROM DUAL;
        RES
-----------
          0

SELECT ACOS(2) res FROM DUAL;
        RES
-----------
       Nan
```

示例2

```sql
SHOW PARAMETER MATH_FUNC_RETURN_DECIMAL

NAME                                           VALUE
---------------------------------------------- --------------------------------
MATH_FUNC_RETURN_DECIMAL                    TRUE

set num 128

SELECT ACOS(-1) res FROM DUAL;
                                    RES
---------------------------------------
3.1415926535897932384626433832795028842

SELECT ACOS(0) res FROM DUAL;
                                  RES
--------------------------------------
                    1.5707963267948966

SELECT ACOS(2) res FROM DUAL;
YAS-04426 the argument value is out of range

SELECT ACOS('nan') res FROM DUAL;
YAS-04426 the argument value is out of range
```
