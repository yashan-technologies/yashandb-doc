```ebnf+diagram
upper::= UPPER "(" expr ")"
```

UPPER函数用于将源字符串[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)中的小写字母转换为大写字母，返回转换后的新字符串。

**expr**

通用表达式，其值须为字符型或可转化为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为CHAR、NCHAR或NVARCHAR类型时，返回值的类型与expr的类型相同，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。  

示例

```sql
SELECT UPPER('Shenzhen nihao') l1, UPPER(NULL) l2 FROM DUAL;
L1                L2
----------------- -----
SHENZHEN NIHAO
```
