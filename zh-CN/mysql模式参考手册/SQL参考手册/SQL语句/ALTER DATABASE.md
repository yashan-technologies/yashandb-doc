## 通用描述

ALTER DATABASE用于修改数据库的相关属性。

该语句优先依据yashan模式的语法规则（即[ALTER DATABASE](../../../开发手册/SQL参考手册/SQL语句/ALTER DATABASE.md)）进行解析。若解析未成功，则按照兼容MySQL语法的规则（即本文所述）进行解析。若两种规则都无法匹配，系统将返回错误提示。

语句定义
----

**alter database::=**

```ebnf
= ALTER DATABASE database_name [DEFAULT] CHARACTER SET charset_name.
```

**charset_name**


|  字符集| 配置原则|
|------------|-------------------------------------------------------------------------------------|
| GBK        | 如果数据库只需要支持中文，数据量很大，性能要求也很高，建议选择双字节定长编码的中文字符集GBK。                                     |
| UTF8      | 如果应用程序需要处理各种各样的文字，或者需要将处理结果发布到不同语言的国家或地区，建议选择Unicode字符集，即UTF8。<br/>此项为YashanDB推荐和默认的字符集。 |
| ASCII      | 如果数据库只需要支持ASCII收录的拉丁系字符，例如英语和部分西欧语言，则可以选择ASCII字符集。                                    |
| GB18030    | 此字符集达到GB18030-2022标准的实现级别三。如果数据库有大量使用中文的场景，且对生僻字的显示、处理、输出有比较严格的要求，可以选择此字符集。<br/>在YashanDB中，GB18030字符集仅支持ASCII范围内的字符进行大小写转换。         |
| BINARY  |此字符集为二进制字符集，按字节进行比较并区分大小写。|
| LATIN1  |单字节字符集，支持西欧语言，每个字符占用1个字节。|
| UTF8MB3  |3字节的UTF8编码字符集，支持多语种平面字符。|
| UTF8MB4  |4字节的UTF8编码字符集，支持完整的Unicode字符集和emoji表情符号。|


示例（单机HEAP表）

```sql
alter database sales default character set 'utf8mb4';

```
