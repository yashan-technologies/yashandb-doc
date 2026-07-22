```ebnf
trim = TRIM "(" [[LEADING|TRAILING|BOTH] trim_character from] expr ")" .
```

TRIM函数用于删除源字符串[expr](../通用SQL语法/expr)的前缀或后缀，返回新的子字符串。

**LEADING|TRAILING|BOTH**

指定删除字符串的前缀|后缀|前后缀，可省略，默认为BOTH。  

**trim\_character**

指定前/后缀的内容，可省略，默认为1个空格。trim_character为与expr相同的通用表达式，其值须为字符型或可转换为字符型的其他类型，在向量化执行引擎中长度可以为多字符，否则长度只能为1字符。

当trim_character的值为NULL时，函数返回NULL。

**expr**

通用表达式，其值须为字符型或除NCLOB类型外的可转换为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- 当expr为CLOB类型时返回值为CLOB类型，当expr为NCHAR/NVARCHAR时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。  

示例

```sql
SELECT TRIM(' bar ') t_default
,TRIM(LEADING 'x' FROM 'xxxbarxxx') t_left
,TRIM(BOTH 'x' FROM 'xxxbarxxx') t_both
,TRIM(TRAILING 'x' FROM 'xxxbarxxx') t_right
FROM DUAL;
T_DEFAULT T_LEFT    T_BOTH T_RIGHT  
--------- --------- ------ ---------
bar       barxxx    bar    xxxbar
```
