```ebnf+diagram
to_multi_byte::= TO_MULTI_BYTE "(" char ")"
```

TO_MULTI_BYTE函数用于将源字符串char中的半角字符（单字节字符）转换成全角字符（多字节字符）。

仅当数据库配置的[字符集](../../全部手册/数据库管理/实例管理/数据库字符集配置)同时包含单字节和多字节字符时，本函数才会生效。

本函数仅适用于HEAP表。

**char**

源字符串，其值须为字符型或可转化为字符型的其他类型。

- 当char为CHAR、VARCHAR类型，函数返回VARCHAR数据类型；char为NCHAR、NVARCHAR类型，返回NVARCHAR数据类型。
- 当char为其他类型时，会先将其隐式转换为VARCHAR类型再进行函数处理，函数返回VARCHAR类型数据。
- 若char中的半角字符不存在对应的全角字符时，函数将直接输出原字符。
- 若char转换成全角字符后超过VARCHAR最大长度时，函数截断字符串，返回转换后最大长度的全角字符串。

示例（HEAP表）

```sql
SELECT TO_MULTI_BYTE('chi na12678976!?>@DEFHIG^') FROM dual;

TO_MULTI_BYTE('CHINA12678976!?>@DEFHIG^')
----------------------------------------------------------------
ｃｈｉ　ｎａ１２６７８９７６！？＞＠ＤＥＦＨＩＧＤ＾

```
