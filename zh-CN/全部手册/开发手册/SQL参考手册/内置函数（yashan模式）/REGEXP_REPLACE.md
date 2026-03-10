```ebnf+diagram
regexp_replace::= REGEXP_REPLACE "(" expr "," regexp ["," replace["," position["," occurrence["," match_para]]]] ")"
```

REGEXP_REPLACE为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp，并使用replace字符串替换第occurrence次匹配到的regexp子串，最后返回替换后的完整字符串，返回值为VARCHAR类型。若没有匹配到，函数返回源字符串expr。

函数首先从expr的第position个字符开始进行匹配，匹配到第1次后，继续从该匹配项后的第1个字符进行匹配，依此类推直到expr的最后一个字符。

本函数与[REPLACE](./REPLACE)函数相似，但本函数的目标字符串regexp可使用正则表达式。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 函数返回结果字符串长度超过上限（65534）将会报错。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

当regexp为NULL时，函数返回expr。

**replace**

用于替代的字符串，replace为与expr相同的通用表达式，须为字符型或可转换为字符型的其他类型。

**position**

指定开始进行匹配的偏移量，可省略，默认为1。position为与expr相同的通用表达式，须为数值型数据，或可转换为NUMBER类型的其他类型数据。

- position的值应为正整数（0或负数将报错，小数将直接截取整数部分），内部处理时统一转换为BIGINT类型。

- 当position超出expr值的长度时，不执行匹配，函数返回expr值。

- 当position为NULL时，函数返回NULL。

**occurrence**

指定按regexp在expr中第occurrence次匹配到的子字符串执行替代，可省略，默认为1。occurrence为与expr相同的通用表达式，须为数值型数据或可转换为NUMBER类型的其他类型数据。

- occurrence的值应为0或正整数（负数将报错，小数将直接截取整数部分），内部处理时统一转换为BIGINT类型。

- 当occurrence为0时，替换所有匹配项。

- 当occurrence超出匹配次数时，不执行匹配，函数返回expr值。

- 当occurrence为NULL时，函数返回NULL。

**match_para**

正则匹配参数，与[REGEXP_COUNT](REGEXP_COUNT.html#match_para)中的含义相同。

示例

```sql
SELECT REGEXP_REPLACE('1234567890', '456','!', 1, 1,'i') "REGEXP_REPLACE" FROM DUAL;
REGEXP_REPLACE
--------------
123!7890

SELECT REGEXP_REPLACE('1234567890', '[0-9]','!', 1, 5) "REGEXP_REPLACE" FROM DUAL;
REGEXP_REPLACE
--------------
1234!67890

SELECT REGEXP_REPLACE(DHQ,'h','H',1,0,'i') "REGEXP_REPLACE"
FROM area
WHERE area_no='01';
REGEXP_REPLACE                                                   
------------------ 
SHangHai  
```
