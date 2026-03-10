```ebnf+diagram
regexp_like::= REGEXP_LIKE "(" expr "," regexp ["," match_para] ")"
```

REGEXP_LIKE为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp并返回匹配结果，返回值为布尔类型，匹配成功时返回TRUE，否则返回FALSE。

本函数与LIKE语法相似，但与LIKE只能使用'%'和'_'通配符相比，本函数的目标字符串regexp可使用正则表达式。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的XMLTYPE、LOB类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

当regexp为NULL时，函数返回NULL。

**match_para**

正则匹配参数，与[REGEXP_COUNT](REGEXP_COUNT.html#match_para)中的含义相同。

示例

```sql
SELECT REGEXP_LIKE('aa\naa','^aa$','i') reg1,
REGEXP_LIKE('aa','^aa$','i') reg2 FROM DUAL;
REG1                 REG2              
-------------------- --------------------
false                true             
 
SELECT REGEXP_LIKE('AA','A.A') reg1,
REGEXP_LIKE('AA','A.A','n') reg2 FROM DUAL;
REG1                 REG2                
-------------------- --------------------
false                false     
 
SELECT REGEXP_LIKE('-654196584','^-[0-9]*[1-9][0-9]*$') reg1,
REGEXP_LIKE('654196584','^[0-9]*[1-9][0-9]*$','m') reg2 FROM DUAL;        
REG1                 REG2                
-------------------- --------------------
true                 true
```

