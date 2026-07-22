```ebnf
ln = LN "(" expr ")".
```

LN函数用于计算[expr](../通用SQL语法/expr)的自然对数，返回一个DOUBLE类型的数值。

expr的值为须为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

基于自然对数的数学概念，expr的值应该为一个正数。下表列示函数对非正数和一些特殊值的返回规则：

| expr      | LN(expr)                       |
|-----------|--------------------------------|
| 负数、-Inf、0 | Invalid argument for logarithm |
| Inf       | Inf                            |
| Nan, -Nan  | Nan                            |

示例（单机HEAP表）

```sql
SELECT LN(2) res;
       res
-----------
6.931E-001
```
