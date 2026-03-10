```ebnf+diagram
strpos::= STRPOS "(" expr "," substring ")"
```

STRPOS函数在源字符串[expr](../通用SQL语法/expr)中从左向右查找目标字符串substring，返回substring在expr中首次出现的位置，返回值为INT类型。若没有匹配到，函数返回0。

**expr**

通用表达式，其值须为字符型或除JSON、LOB类型外的可转化为字符型的其他类型。

- 当expr的值为CHAR类型时，会先去除字符串尾部的空格。

- 当expr的值为NULL时，函数返回NULL。

**substring**

需查找的字符串表达式，substring为与expr相同的通用表达式，须为字符型或除JSON、LOB类型外的可转化为字符型的其他类型。

- 当substring的值为CHAR类型时，会先去除字符串尾部的空格，若substring为全空格串（例如`'   '`），函数返回1。

- 当substring的值为NULL时，函数返回NULL。

示例

```sql
SELECT STRPOS('abcdef', 'abc') a
,STRPOS('abcdef', 'def') b
,STRPOS('Guangdong——Shenzhen', 'en') c
,STRPOS('  ',  '  ') d
,STRPOS(NULL, NULL) e
FROM DUAL;
           A            B            C            D            E
------------ ------------ ------------ ------------ ------------
           1            4           14            1
```
