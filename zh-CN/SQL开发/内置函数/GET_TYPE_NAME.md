```ebnf+diagram
get_type_name::= GET_TYPE_NAME "(" expr ")"
```

GET_TYPE_NAME函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)作为数据类型ID，用于查询类型的名称，并将结果作为VARCHAR类型的字符串返回。

本函数不支持向量化计算。

**expr**

expr须为除BIT外数值型数据，或可转换为INTEGER的字符型数据，取值范围[0,254]。对于其他类型，函数返回类型不支持。

expr不能为NULL，当expr的值为NULL时，函数报错。

示例（单机HEAP表）

```sql
SELECT GET_TYPE_NAME(4) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
INTEGER

SELECT GET_TYPE_NAME(5.21) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
BIGINT

SELECT GET_TYPE_NAME(5.71) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
UTINYINT

SELECT GET_TYPE_NAME('26') TYPE_NAME FROM DUAL;

TYPE_NAME
---------
VARCHAR
```
