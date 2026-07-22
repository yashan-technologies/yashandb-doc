```ebnf
soundex = SOUNDEX "(" expr ")".
```

SOUNDEX函数返回其参数expr[通用表达式](../通用SQL语法/expr)的soundex值，expr须为字符型或可以转换为字符型的其他类型。函数将返回一个VARCHAR/NVARCHAR类型的字符串。 soundex值为英语发音下单词的特殊缩略。

本函数遵循如下规则：

- 本函数不支持向量化计算。
- 当expr为NCHAR、NVARCHAR和NCLOB时，返回值为NVARCHAR类型；当expr为其他类型时，返回值为VARCHAR类型。
- expr不能为超过65534字节的BFILE、LOB类型数据。
- 当expr隐式转换成字符型后的值中不包含英文字符时，函数返回NULL。
- 当expr为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT SOUNDEX(NULL) from dual;

SOUNDEX(NULL)
------------- 

SELECT SOUNDEX('SSHS') from dual;

SOUNDEX('SSHS')
--------------- 
       S200
SQL> SELECT SOUNDEX('SMYTHE') from dual;

SOUNDEX('SMYTHE')
----------------- 
       S530
SELECT SOUNDEX('SMITH') from dual;

SOUNDEX('SMITH')
---------------- 
       S530  
```
