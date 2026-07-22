```ebnf
regexp_substr = REGEXP_SUBSTR "(" expr "," regexp ["," position["," occurrence["," match_para["," subexpr]]]] ")".
```

REGEXP\_SUBSTR为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp，返回第occurrence次匹配到regexp的第subexpr个子字符串，返回值为VARCHAR类型。若没有匹配到，函数返回NULL。

函数首先从expr的第position个字符开始进行匹配，匹配到第1次后，继续从该匹配项后的第1个字符进行匹配，依此类推直到expr的最后一个字符。

本函数的功能与参数定义均类似于[REGEXP_INSTR](./REGEXP_INSTR)。

**expr**

expr的值须为字符型或可转换为字符型的其他类型。

- expr不能为转义字符。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

- regexp支持转义字符。

- 当regexp为NULL时，函数返回NULL。

**position**

指定开始进行匹配的偏移量，可省略，默认为1。position为与expr相同的通用表达式，须为数值型数据，或可转换为NUMBER类型的其他类型数据。

- position的值应为正整数（0或负数将报错，小数将直接截取整数部分），内部处理时统一转换为BIGINT类型。

- 当position超出expr值的长度时，不执行匹配，函数返回NULL。

- 当position为NULL时，函数返回NULL。

**occurrence**

指定返回regexp在expr中第occurrence次匹配到的子字符串，可省略，默认为1。occurrence为与expr相同的通用表达式，须为数值型数据或可转换为NUMBER类型的其他类型数据。

- occurrence的值应为正整数（0或负数将报错，小数将直接截取整数部分），内部处理时统一转换为BIGINT类型。

- 当occurrence超出匹配次数时，不执行匹配，函数返回NULL。

- 当occurrence为NULL时，函数返回NULL。

**match\_para**

正则匹配参数，与[REGEXP_COUNT](REGEXP_COUNT.md#match_para)中的含义相同。

**subexpr**

当匹配到regexp时，函数返回regexp的第subexpr个子表达式（即子字符串），可省略，默认为0，表示返回完整的regexp字符串。

子表达式是指使用括号`()`包围的表达式片段，括号允许嵌套，所有子表达式按其左括号在表达式中出现的顺序进行编号，子表达式编号从1开始（0则表示整个regexp表达式）。例如`0123(((abc)(de)f)ghi)45(678)`包含5个子表达式，按顺序分别为“abcdefghi”、“abcdef”、“abc”、“de”和“678”。

- subexpr的值只能为[0,9]区间的整数，值小于0将报错，值大于9将返回NULL，小数将直接截取整数部分。

- 当subexpr超过regexp子表达式数量时，函数返回NULL。

示例

```sql
SELECT REGEXP_SUBSTR('1234567890', '(123)(4(56)(78))', 1, 1,'i', 2) "REGEXP_SUBSTR" FROM DUAL;
REGEXP_SUBSTR
--------------
45678

SELECT REGEXP_SUBSTR('1234567890', '[0-9]', 1, 5) "REGEXP_SUBSTR" FROM DUAL;
REGEXP_SUBSTR
-------------
5
```
