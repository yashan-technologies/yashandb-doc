```ebnf+diagram
tanh::= TANH  "(" expr ")" 
```

TANH函数返回其参数的双曲正切，参数大小本身无限制（只受限于其所属数据类型所规定范围），函数返回一个DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT TANH(1) res FROM DUAL;
RES
-----------
7.616E-001

SELECT TANH('1') res FROM DUAL;
RES
-----------
7.616E-001

SELECT TANH(b'1') res FROM DUAL;
RES
-----------
7.616E-001
```
