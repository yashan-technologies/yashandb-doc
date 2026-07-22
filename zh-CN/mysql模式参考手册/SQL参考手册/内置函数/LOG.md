```ebnf
log = LOG "(" expr1 ["," expr2] ")".
```
LOG函数入参个数为1时，是[LN](./LN)函数的同义词。

LOG函数入参个数为2时，计算expr2以expr1为底的对数，返回一个DOUBLE类型的数值。

expr1和expr2均为YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr1或者expr2的值为NULL时，函数返回NULL。

根据对数的数学概念，expr1应该为除0和1以外的正数，expr2应该为任意一个正数，除此之外的其他情况函数处理规则见下表：

| expr1       | LOG(expr1, expr2)              |
|-------------|--------------------------------|
| 负数、-Inf、0、1 | Invalid argument for logarithm |
| Inf         | Inf                            |
| Nan, -Nan    | Nan                            |

| expr2     | LOG(expr1, expr2)              |
|-----------|--------------------------------|
| 负数、-Inf、0 | Invalid argument for logarithm |
| Inf       | Inf                            |
| Nan, -Nan  | Nan                            |

示例（HEAP表）

```sql
SELECT LOG(2,4) res;
        res
-----------
   2.0E+000

-- example via yasql
SELECT LOG(2) res;
        res
-----------
 6.931E-001

-- example via MySQL Client
SELECT LOG(2) res;
+-------------------------+
| res                     |
+-------------------------+
| 6.9314718055994529E-001 |
+-------------------------+

```
