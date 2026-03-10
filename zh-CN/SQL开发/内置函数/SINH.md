```ebnf+diagram
sinh::= SINH  "(" expr ")" 
```

SINH函数返回其参数的双曲正弦值，参数大小本身无限制（只受限于其所属数据类型所规定范围），函数返回一个DOUBLE类型数据。

其中[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT SINH(2) res FROM DUAL;
RES
-----------
3.627E+000

SELECT SINH('2') res FROM DUAL;
RES
-----------
3.627E+000

SELECT SINH(b'10') res FROM DUAL;
RES
-----------
3.627E+000
```
