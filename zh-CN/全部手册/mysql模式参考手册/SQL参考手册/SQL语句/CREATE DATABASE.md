## 通用描述

CREATE DATABASE语句用于创建一个新的数据库，该语句只能在数据库启动至open阶段且切换至mysql模式后执行。

## 语句定义

**create database::=**

```ebnf+diagram
syntax::= CREATE DATABASE [IF NOT EXISTS] database_name [[DEFAULT] CHARACTER SET charset_name [[DEFAULT] COLLATE collation_name]] 
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


**collation_name**



|  字符序| 说明|
|--------------------| ------------------------------------------------------------ |
| ASCII_BIN          | 将字符作为二进制数据进行比较和排序。                         |
| ASCII_GENERAL_CI   | 字母不区分大小写，比较和排序时将大写字母和小写字母视为相同的字符。 |
| GBK_BIN            | 使用二进制排序规则。                                         |
| GBK_CHINESE_CI     | 使用中文语言排序规则。                                       |
| UTF8MB4_BIN        | 使用二进制排序规则。                                         |
| UTF8MB4_GENERAL_CI/UTF8_GENERAL_CI | 使用通用排序规则。           |
| LATIN1_BIN         | 拉丁字母字符集使用二进制排序规则。                           |
| LATIN1_GENERAL_CI  | 使用大小写不敏感的通用排序规则。                             |
| GB18030_BIN        | 使用二进制排序规则。                                         |
| GB18030_CHINESE_CI | 使用拼音排序规则，不区分大小写。                             |



示例（单机HEAP表）

```sql
CREATE DATABASE IF NOT EXISTS sales DEFAULT CHARACTER SET `utf8` DEFAULT collate `utf8mb4_general_ci`;
```
