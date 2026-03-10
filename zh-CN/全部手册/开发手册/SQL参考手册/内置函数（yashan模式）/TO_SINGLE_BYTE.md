```ebnf+diagram
to_single_byte::= TO_SINGLE_BYTE "(" char ")"
```

TO_SINGLE_BYTE函数用于将源字符串char中的全角字符（多字节字符）转换成半角字符（单字节字符）。

仅当数据库配置的[字符集](../../../数据库管理/基本数据库管理/字符集配置)同时包含单字节和多字节字符时，本函数才会生效。

本函数不支持向量化计算。

**char**

源字符串，其值须为字符型或可转化为字符型的其他类型。

- 当char为CHAR、VARCHAR类型，函数返回VARCHAR数据类型；char为NCHAR、NVARCHAR类型，返回NVARCHAR数据类型。

- 当char为其他类型时，会先将其隐式转换为VARCHAR类型再进行函数处理，函数返回VARCHAR类型数据。

- 若char中的全角字符不存在对应的半角字符时，函数将直接输出原字符。

示例（HEAP表）

```sql
SELECT TO_SINGLE_BYTE('ｃｈｉ　ｎａ１２６７8976！？＞＠ＤＥＦＨＩＧ＾') FROM dual;

TO_SINGLE_BYTE('ＣＨＩ　ＮＡ１２６７8976！？＞＠
--------------------------------------------------------------
chi na12678976!?>@DEFHIG^

```
