```ebnf+diagram
trim::= TRIM "(" [[LEADING|TRAILING|BOTH] trim_expr from] expr ")" 
```

TRIM函数用于删除源数据[expr](../通用SQL语法/expr)的前缀或后缀并返回删除后的新数据，返回值类型与源数据一致。

本函数遵循如下规则：

- 当trim_expr、expr任意一个为二进制类型（BINARY、BLOB）或BIT类型时，函数将按二进制类型处理并且返回值为二进制类型数据，否则将按字符串类型处理并且返回值为字符串类型数据。

- 返回值类型是否为LOB取决于expr长度是否超过65534。

**LEADING|TRAILING|BOTH**

指定删除字符串的前缀|后缀|前后缀，可省略，默认为BOTH。  

**trim_expr**

指定前/后缀的内容，可省略，默认为1个空格。trim_expr为与expr相同的通用表达式，其值不能为JSON类型。

- 当trim_expr的值为NULL时，函数返回NULL。

**expr**

expr为通用表达式，其值不能为JSON类型。

- 当expr的值为NULL时，函数返回NULL。  

示例（单机HEAP表）

```sql
SELECT TRIM('a中cd' FROM 'a中cdgklla中cd');

trim('a中cd' from 'a中cdgklla中cd')               
------------------------------------------------- 
gkll                                             


SELECT TRIM(LEADING 'a中cd' FROM 'a中cdgklla中cd');

trim(leading 'a中cd' from 'a中cdgklla中cd')       
------------------------------------------------- 
gklla中cd                                        


SELECT TRIM(trailing 'a中cd' FROM 'a中cdgklla中cd');

trim(trailing 'a中cd' from 'a中cdgklla中cd')      
------------------------------------------------- 
a中cdgkll                                        

```