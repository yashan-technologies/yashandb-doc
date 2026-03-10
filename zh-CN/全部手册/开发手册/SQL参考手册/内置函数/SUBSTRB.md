```ebnf+diagram
substrb::= SUBSTRB "(" expr "," pos ["," len] ")"
```

SUBSTRB函数用于在源字符串[expr](../通用SQL语法/expr)的pos位置提取长度为len的子字符串。

本函数不支持向量化计算。

**expr**

通用表达式，其值须为字符型或除NCLOB类型外的可转换为字符型的其他类型。

- expr不能为超过65534字节的LOB类型数据。

- 当expr的值为RAW类型时返回值为RAW类型，当expr值为NCHAR/NVARCHAR类型时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**pos**

表示从pos值指定位置开始提取字符串，pos为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER的其他类型数据，取值范围为[-2147483648,2147483647]。值为正数表示从前往后确定起始位置，值为负数表示从后向前确定起始位置。

- 当其值为带有小数的NUMBER类型（或转换后为NUMBER类型）时，函数截断其小数位保留整数位。

- 当其值为浮点类型时，函数将其奇进偶舍取整。

- 当pos的值为NULL时，函数返回NULL。

- 当pos值为0时，等同于1。

- 当pos绝对值超过字符串的长度时，函数返回NULL。

**len**

表示提取len值指定长度的字符串，可省略。len为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER的其他类型数据，取值范围为[-2147483648,2147483647]。

- 当len的值为带有小数的NUMBER类型（或转换后为NUMBER类型）时，函数截断其小数位保留整数位。

- 当len的值为浮点类型时，函数将其奇进偶舍取整。

- 当不指定len，或len的值大于从pos值指定位置至源字符串末尾的长度时，函数返回从pos值指定位置开始至源字符串末尾的子字符串。

- 当len的值为0或负数时，函数返回NULL。

- 当len的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT SUBSTRB('——abc', 4) a1
,SUBSTRB('Aabc', 4) a2
,SUBSTRB('——abc', 3, 4) b1
,SUBSTRB('Aabc', 3, 4) b2
,SUBSTRB('——abc', -3) c1
,SUBSTRB('Aabc', -3) c2
,SUBSTRB('——abc', 3, -4) d1
,SUBSTRB('Aabc', 3, -4) d2
FROM DUAL;
A1        A2    B1    B2    C1    C2    D1    D2
--------- ----- ----- ----- ----- ----- ----- -----
—abc      c      —    bc    abc   abc
 
SELECT SUBSTRB('abcdefg', CAST(2.5 AS NUMBER), CAST(1.5 AS NUMBER)) a
,SUBSTRB('abcdefg', '2.5', '1.5') b
,SUBSTRB('abcdefg', CAST(2.5 AS FLOAT), CAST(1.5 AS FLOAT)) c
,SUBSTRB('abcdefg', CAST(2.5 AS DOUBLE), CAST(1.5 AS DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
b         b     bc        bc
```
