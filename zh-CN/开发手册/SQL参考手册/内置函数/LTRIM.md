```ebnf
ltrim = LTRIM "(" expr ["," trim_character] ")".
```

LTRIM函数从左往右删除[expr](../通用SQL语法/expr)的值里与trim\_character匹配的内容，得到一个新的子字符串。

**expr**

通用表达式，其值须为字符型或除NCLOB外的可转换为字符型的其他类型。

- 当expr为CLOB类型时，返回值为CLOB类型，当expr为NCHAR/NVARCHAR时，返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。
- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。
- 当expr的值为NULL时，函数返回NULL。

**trim\_character**

指定要匹配的内容，可省略。trim_character为与expr相同的通用表达式，须为字符型或可转换为字符型的其他类型。

- 省略trim_character时，默认的匹配内容为1个空格。
- 指定trim_character时，函数将会从左至右逐一对比expr与trim_character中的字符，若expr中的字符在trim_character中存在则在expr中将其删除，直到遇到首个不存在于trim_character中的字符时停止。若全部字符均被匹配删除，函数返回空字符串。
- 在向量化执行引擎中，trim_character不能为LOB类型的行外存储数据。
- 当trim_character的值为NULL时，函数返回NULL。  

示例

```sql
-- 从左开始的3个'3'均能在'33'中找到，因此被删除，直到遇到'1'不能在'33'中找到，停止匹配并返回剩余字符串
SELECT LTRIM('33311333','33') res FROM DUAL;
RES
--------------------
11333    
 
-- 从左至右所有字符均能在'313'中找到，全部被删除，返回空字符串
SELECT LTRIM('33311333','313') res FROM DUAL;
RES
--------------------
                     
-- 只有一个参数时按空格执行匹配删除
SELECT LTRIM(' 11333') res FROM DUAL;
RES
--------------
11333
```
