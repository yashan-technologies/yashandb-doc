```ebnf
substring = SUBSTRING "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")".
```

SUBSTRING函数用于在源字符串[expr](../通用SQL语法/expr)的指定位置提取指定长度的子字符串。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- 在向量化执行引擎中，expr为LOB类型时，输出的最大长度不能超过32000字节。

- 当expr的值为CLOB/NCLOB类型时返回值与expr的值类型相同，当expr的值为NCHAR/NVARCHAR类型时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**pos**

表示从pos值指定位置开始提取字符串，pos为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER的其他类型数据，取值范围为[-2147483648,2147483647]。值为正数表示从字符串的头部开始确定起始位置，值为负数从字符串的尾部开始确定起始位置。

- 当pos的值为带有小数的NUMBER类型（或转换后为NUMBER类型）时，函数将其四舍五入取整。

- 当pos的值为浮点类型时，函数将其奇进偶舍取整。

- 当pos的值等于0或其绝对值超过字符串的长度时，函数返回NULL。

- 当pos的值为NULL时，函数返回NULL。

**len**

表示提取len值指定长度的字符串，可省略。len为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER的其他类型数据，取值范围为[-2147483648,2147483647]。

- 当len的值为带有小数的NUMBER类型（或转换后为NUMBER类型）时，函数将其四舍五入取整。

- 当len的值为浮点类型时，函数将其奇进偶舍取整。

- 当不指定len，或len的值大于从pos值指定位置至源字符串末尾的长度时，函数返回从pos值指定位置开始至源字符串末尾的子字符串。

- 当len的值为0或负数时， 函数返回NULL。

- 当len的值为NULL时，函数返回NULL。

示例

```sql
SELECT SUBSTRING('abcdefg', 3) a
,SUBSTRING('abcdefg', 3, 2) b
,SUBSTRING('abcdefg', -3) c
,SUBSTRING('abcdefg', 3, -2) d
FROM DUAL;
A         B     C     D
--------- ----- ----- -----
cdefg     cd    efg

SELECT SUBSTRING('abcdefg' from 3) a
,SUBSTRING('abcdefg' from 3 for 2) b
,SUBSTRING('abcdefg' from -3) c
,SUBSTRING('abcdefg' from 3 for -2) d
FROM DUAL;
A         B     C     D
--------- ----- ----- -----
cdefg     cd    efg

SELECT SUBSTRING('abcdefg', CAST(2.5 as NUMBER), CAST(1.5 as NUMBER)) a
,SUBSTRING('abcdefg', '2.5', '1.5') b
,SUBSTRING('abcdefg', CAST(2.5 as FLOAT), CAST(1.5 AS FLOAT)) c
,SUBSTRING('abcdefg', CAST(2.5 as DOUBLE), CAST(1.5 as DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
cd        b     bc        bc

SELECT SUBSTRING('abcdefg' FROM CAST(2.5 as NUMBER) FOR CAST(1.5 as NUMBER)) a
,SUBSTRING('abcdefg' FROM '2.5' for '1.5') b
,SUBSTRING('abcdefg' FROM CAST(2.5 as FLOAT) FOR CAST(1.5 AS FLOAT)) c
,SUBSTRING('abcdefg' FROM CAST(2.5 as DOUBLE) FOR CAST(1.5 as DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
cd        b     bc        bc
```
