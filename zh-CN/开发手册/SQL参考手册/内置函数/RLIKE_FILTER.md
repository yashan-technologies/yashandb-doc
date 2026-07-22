```ebnf
rlike_filter = RLIKE_FILTER "(" expr "," regexp ")".
```

RLIKE_FILTER为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp并返回匹配结果，返回值为布尔类型，匹配成功时返回TRUE，否则返回FALSE。

本函数与LIKE语法相似，但与LIKE只能使用'%'和'_'通配符相比，本函数的目标字符串regexp可使用正则表达式。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- 当expr的值为NULL时，函数返回NULL。
- expr不能为超过65534字节的BFILE、LOB类型数据。
- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

当regexp为NULL时，函数返回NULL。

示例

```sql
SELECT RLIKE_FILTER('aa\naa','^aa$') reg FROM DUAL;
REG                              
--------------------
false             

SELECT RLIKE_FILTER('-654196584','^-[0-9]*[1-9][0-9]*$') reg FROM DUAL;        
REG                                 
--------------------
true
```
