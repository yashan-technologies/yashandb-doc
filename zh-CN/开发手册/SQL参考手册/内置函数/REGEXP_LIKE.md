```ebnf
regexp_like = REGEXP_LIKE "(" expr "," regexp ["," match_para] ")".
```

REGEXP_LIKE为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp并返回匹配结果，返回值为布尔类型，匹配成功时返回TRUE，否则返回FALSE。

本函数与LIKE语法相似，但与LIKE只能使用'%'和'_'通配符相比，本函数的目标字符串regexp可使用正则表达式。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

当regexp为NULL时，函数返回NULL。

**match\_para**



正则匹配参数，可省略或指定为NULL，默认为'c'，参数可选值如下：

- 'i'：大小写不敏感。

- 'c'：默认值，大小写敏感。

- 'n'：允许句点（.）匹配任何字符，包括换行符。默认情况下，句点与换行符不匹配。

- 'm'：将字符串视为多行，将^和$分别解释为字符串中任意行的开始和结束，而不只是整个字符串的开始或结束。默认情况下，将字符串视为一行。

- 'x'：忽略空白和#注释。默认情况下，空白字符与其自身匹配。

match\_para参数指定为可选值以外的其他值时，函数将返回YAS-07202错误。



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
