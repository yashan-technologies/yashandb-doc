```ebnf+diagram
lower::= LOWER "(" expr ")"
```

LOWER函数将[expr](../通用SQL语法/expr)的值中的大写字母转换为小写。此函数是[LCASE](LCASE)函数的同义词。

**expr**

通用表达式，其值须为二进制类型、字符型或可转换为字符型的其他类型。

- 当expr为二进制类型时，函数不进行大小写转换直接返回原数据。

- expr不能为超过65534字节的字符型数据。

- 当expr的值为NULL时，函数返回NULL。

**返回值类型**

- 当expr为字符类型时，返回值为与之对应的变长字符类型。

- 当expr为除BLOB外的二进制类型时，返回值长度若小于65534则为raw类型，否则为blob类型。

- 当expr为BLOB类型时，返回值为blob类型。

- 其余场景返回值均为VARCHAR类型。

示例（单机HEAP表）

```sql
SELECT LOWER('Shenzhen NIHAO') l1, LOWER(NULL) l2 FROM DUAL;
l1            l2
------------- -----
shenzhen nihao     
```
