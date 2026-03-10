```ebnf+diagram
replace::= REPLACE "(" expr "," search_expr ","  replace_expr ")"
```

REPLACE函数将源数据[expr](../通用SQL语法/expr)中所有的search_expr指定内容替换为replace_expr指定内容并返回替换后的新数据。

本函数遵循如下规则：

- 当expr、search_expr、replace_expr任意一个为二进制类型（BINARY、BLOB）或BIT类型时，函数将按二进制类型处理并且返回值为二进制类型数据，否则将按字符串类型处理并且返回值为字符串类型数据。

- 返回值类型是否为LOB取决于替换结果长度是否超过65534。

**expr**

expr为通用表达式，其值不能为JSON类型。

- 当expr的值为NULL时，函数返回NULL。  

**search_expr**

需要被替换掉的目标内容，search_expr为与expr相同的通用表达式，须为字符型或除JSON类型外的可转化为字符型的其他类型。

- 当search_expr的值为NULL时，函数返回NULL。 

**replace_expr**

按此字符串的值进行替换，replace_expr为与expr相同的通用表达式，不支持JSON类型。

- 当replace_expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT REPLACE('abb','ab','a');

replace('abb','ab','a') 
----------------------- 
ab                     

```
