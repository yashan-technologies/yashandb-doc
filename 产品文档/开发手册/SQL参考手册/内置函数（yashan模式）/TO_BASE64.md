```ebnf+diagram
to_base64::= TO_BASE64 "(" expr ")"
```

TO_BASE64函数将[expr](../通用SQL语法/expr)所表示的字符串使用BASE64进行编码，并返回编码后的VARCHAR类型字符串数据。

本函数不支持向量化计算。

**expr**

通用表达式，其值须为字符型或可转化为字符型的其他类型（LOB、XMLTYPE类型支持隐式转换）。

- expr不能为超过65534字节的XMLTYPE、LOB类型数据。

- 当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT TO_BASE64('ABC') RES FROM DUAL;

RES   
----- 
QUJD 
```
