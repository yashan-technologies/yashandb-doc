```ebnf+diagram
md5::= MD5 "(" expr ")"
```

MD5函数用于计算[expr](../通用SQL语法/expr)的MD5值。expr的值须为字符型或可转换为字符型的其他类型。返回一个varchar(32)类型的固定长度的十六进制字符串。

本函数遵循如下规则：

- 当expr的值为NULL或空串时，函数返回值为NULL。

- 当expr为nchar类型或做变量的char类型时，函数会将其末尾的空格进行消除，再计算MD5值。

- 当expr为double或float类型，函数返回其科学计数法的MD5值。

- 当expr为bool类型，函数返回其对应的bool类型数据值（1和0）的MD5值。

- expr不能为超过65534字节的XMLTYPE、LOB类型数据。

- 本函数不支持向量化计算。

示例（HEAP表）

```sql

SELECT MD5(1) FROM DUAL;

MD5(1)
---------------------------------
c4ca4238a0b923820dcc509a6f75849b
    
SELECT MD5(NULL) FROM DUAL;

MD5(NULL)
---------

SELECT MD5('') FROM DUAL;

MD5('')
-------

SELECT MD5(' ') FROM DUAL;

MD5('')
---------------------------------
7215ee9c7d9dc229d2921a40e899ec5f
```