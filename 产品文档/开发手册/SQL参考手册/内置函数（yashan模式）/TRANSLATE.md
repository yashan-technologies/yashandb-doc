```ebnf+diagram
translate::= TRANSLATE ("(" expr "," from_string "," to_string ")" | "(" USING (CHAR_CS | NCHAR_CS) ")")
```

TRANSLATE函数具有如下用法：

- 用法一：用于将源字符串[expr](../通用SQL语法/expr)中的所有的from_string中的每一个字符一对一替换为to_string中的字符，返回新的字符串。

- 用法二：即TRANSLATE……USING用法，TRANSLATE函数用于对源字符串expr按指定编码方式进行字符集转换，返回对应类型的字符串。

本函数遵循如下规则：

- 本函数不支持向量化计算。

- 用法一还遵循如下规则：
    
    - from_string字符串与源字符串expr匹配时，区分大小写。

    - 当from_string字符串长于to_string字符串时，from_string超出部分的每个字符将直接从新字符串中删除。

    - expr为NCLOB/NCHAR/NVARCHAR时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的XMLTYPE、LOB类型数据。

- 当expr的值为NULL时，函数返回NULL。

**from_string**

需要被替换的字符串，from_string为与expr相同的通用表达式，须为字符型数据或可转换为字符型的其他类型数据（LOB、XMLTYPE类型支持隐式转换）。

当from_string的值为NULL时，函数返回NULL。

**to_string**

将要替换成的字符串，按此字符串的值进行一对一替换，to_string为与expr相同的通用表达式，须为字符型数据或可转换为字符型的其他类型数据（LOB、XMLTYPE类型支持隐式转换）。

当to_string的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
-- 一对一替换，即'c'换成'a'、'h'换成'b'
SELECT TRANSLATE('I am chinese', 'ch', 'ab') TRANSLATE FROM DUAL;
TRANSLATE
-------------
I am abinese

-- 'china'长于'ab'，超出部分的'i'、'n'以及'a'将直接从新字符串中删除
SELECT TRANSLATE('I am chinese', 'china', 'ab') TRANSLATE FROM DUAL;
TRANSLATE
-------------
I m abese

SELECT TRANSLATE('我是中国人,我爱中国,China', '中国', 'China') TRANSLATE FROM DUAL;
TRANSLATE
-----------------------------
我是Ch人,我爱Ch,China
```

**translate_using**

用于指定字符串编码方式：

- CHAR_CS：表示将expr转换成用数据库字符集编码的字符串，函数返回值将为VARCHAR类型。

- NCHAR_CS：表示将expr转换成用国家字符集编码的字符串，函数返回值将为NVARCHAR类型。

示例（HEAP表）

```sql
SELECT TRANSLATE('Yashan Database' USING CHAR_CS) TRANSLATE
FROM DUAL;
TRANSLATE
-----------------
Yashan Database

SELECT TRANSLATE('崖山数据库' USING NCHAR_CS) TRANSLATE
FROM DUAL;
TRANSLATE
----------------------------------------------------------------
崖山数据库
```

