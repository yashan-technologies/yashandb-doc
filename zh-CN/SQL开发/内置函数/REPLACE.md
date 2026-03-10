```ebnf+diagram
replace::= REPLACE "(" expr "," search_character [","  replace_character] ")"
```

REPLACE函数将源字符串[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)中所有的search_character替换为replace_character，返回一个新的字符串。

**expr**

expr的值须为字符型、CLOB/NCLOB/BFILE或除JSON、BLOB类型外的可转化为字符型的其他类型。

- 当expr的值为CLOB/NCLOB类型时函数返回值与expr的值类型相同，其余场景函数返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。  

**search_character**

要进行替换的字符串，search_character为与expr相同的通用表达式，须为字符型或除JSON、BLOB类型外的可转化为字符型的其他类型。

当search_character的值为NULL时，函数不执行任何替换。

**replace_character**

按此字符串的值进行替换，replace_character为与expr相同的通用表达式，须为字符型或除JSON、BLOB类型外的可转化为字符型的其他类型。

- 当replace_character的值为NULL时，函数将删除expr中的search_character部分。

- 不指定replace_character时，默认替换值为NULL。

示例

```sql
SELECT REPLACE('ShenZhen', 'Zhen', 'Yang') REPLACE FROM DUAL;
REPLACE  
---------
ShenYang
 
--不执行任何替换
SELECT REPLACE('ShenZhen', '', 'Yang') REPLACE FROM DUAL;
REPLACE  
---------
ShenZhen
 
--替换值为空时，删除要替换的字符串
SELECT REPLACE('ShenZhen', 'Zhen') REPLACE FROM DUAL;
REPLACE
-------
Shen
```
