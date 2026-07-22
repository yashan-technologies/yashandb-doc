```ebnf
split = SPLIT "(" expr "," delimiter "," n ")".
```

SPLIT函数将[expr](../通用SQL语法/expr)表示的字符串按照指定的分割符delimiter分割为若干个子字符串，返回第n个子字符串。

**expr**

通用表达式，其值须为字符型或除JSON、LOB、BFILE类型外的可转化为字符型的其他类型。

- 当expr的值为CHAR类型时，会先去除字符串尾部的空格。

- 当expr的值为NULL时，函数返回NULL。

**delimiter**

分割符，delimiter为与expr相同的通用表达式，须为字符型或除JSON、LOB、BFILE类型外的可转化为字符型的其他类型。

- 当delimiter的值为CHAR类型时，会先去字符串尾部的空格，若为全空格串（例如`'   '`）则最终将处理为1个空格（即`' '`）。

- 当delimiter的值为NULL时，函数返回NULL。

**n**

指定返回的子字符串序号，n为与expr相同的通用表达式，须为数值型或可转换为NUMBER的字符型数据，取值范围为[-2147483648,0)∪(0,2147483647]，正数表示按从左往右的顺序，负数则表示按从右往左的顺序。

- 当n为0时，函数将会报错。

- 当n为小数时，将直接截断小数部分保留整数位。

- 当n的绝对值大于分割后的子字符串个数时，函数返回NULL。

- 当n的值为NULL时，函数返回NULL。

示例

```sql
SELECT SPLIT('a,b,c', ',', 1) a
,SPLIT('a,b,c', ',', 4) b
,SPLIT('Guangdong——Shenzhen', '——', 1) c
,SPLIT('a,b,c', ',' , 2.9) d
FROM DUAL;
A     B     C             D
----- ----- ------------- -----
a           Guangdong     b
```
