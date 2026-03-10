```ebnf+diagram
lower::= LOWER "(" expr ")"
```

LOWER函数将[expr](../通用SQL语法/expr)的值中的大写字母转换为小写，返回一个新字符串。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为CHAR、NCHAR或NVARCHAR类型时，返回值与expr同类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT LOWER('Shenzhen NIHAO') l1, LOWER(NULL) l2 FROM DUAL;
L1            L2    
------------- ----- 
shenzhen nihao       
```
