```ebnf
regexp_count = REGEXP_COUNT "(" expr "," regexp ["," position["," match_para]] ")".
```

REGEXP_COUNT为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)中按正则模式匹配目标字符串regexp，返回匹配到的次数，返回值为BIGINT类型。若没有匹配到，函数返回0。

函数首先从expr的第position个字符开始进行匹配，匹配到第1次后，继续从该匹配项后的第1个字符进行匹配，依此类推直到expr的最后一个字符。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

当regexp为NULL时，函数返回NULL。

**position**

指定开始进行匹配的偏移量，可省略，省略时默认position为1。position为与expr相同的通用表达式，须为数值型数据或可转换为NUMBER类型的其他类型数据。

- position的值应为正整数（0或负数将报错，小数将直接截取整数部分），内部处理时统一转换为BIGINT类型。

- 当position超出expr值的长度时，不执行匹配，函数返回0。

- 当position为NULL时，函数返回NULL。

<span id="match_para" name="match_para"></span>

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
SELECT REGEXP_COUNT('123123123123123', '123', 1, 'i') REGEXP_COUNT FROM DUAL;
         REGEXP_COUNT
---------------------
                    5

SELECT REGEXP_COUNT('123123123123123', '123', 4, 'i') REGEXP_COUNT FROM DUAL; 
         REGEXP_COUNT
---------------------
                    4

SELECT regexp_count('A1B2C3', '[A-Z][0-9]') REGEXP_COUNT FROM DUAL;
         REGEXP_COUNT 
--------------------- 
                    3
```
