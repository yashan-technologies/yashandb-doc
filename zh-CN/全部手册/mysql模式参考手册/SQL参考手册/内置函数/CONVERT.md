```ebnf+diagram
convert::= CONVERT "("expr ("," type_name [charset_info]|USING transcoding_name) ")"
charset_info::=  (CHARACTER SET charset_name|ASCII)
```

CONVERT函数将[expr](../通用SQL语法/expr)的值转换为指定的数据类型或指定的字符集，并按新的类型或者通过字符集转换后的字符串形式返回结果。

YashanDB支持如下情况的类型转换：

|  expr的类型| 目标类型type_name|
| --- | --- |
| BOOLEAN | TINYINT, SMALLINT, INT, BIGINT, CHAR, VARCHAR, NCHAR, NVARCHAR |
| BIT | 除FLOAT、DOUBLE外的数值型、字符型数据 |
| CHAR、VARCHAR | 除UDT外的所有数据类型 |
| NCHAR、NVARCHAR | 除UDT外的所有数据类型 |
| DATE | CHAR, VARCHAR, TIMESTAMP |
| FLOAT、DOUBLE | 除BIT外的数值型、字符型数据 |
| NUMBER | 所有数值型、字符型 |
| TIMESTAMP | 字符型数据、DATE |
| TINYINT、SMALLINT、INT、BIGINT | 所有数值型、字符型 |
| INTERVAL YEAR TO MONTH | 字符型数据 |
| INTERVAL DAY TO SECOND | 字符型数据 |
| CLOB | 字符型数据、JSON |
| NCLOB | 字符型数据、JSON |
| BLOB | 字符型数据、JSON、RAW、UROWID |
| ROWID | 字符型数据、RAW、UROWID |
| UROWID | 字符型数据、RAW |
| JSON | 字符型数据、CLOB、BLOB、NCLOB、RAW |
| RAW | 字符型数据、CLOB、BLOB、NCLOB、UROWID |

除上述类型转换规则外，本函数还遵循如下规则：

* expr的内容需符合目标类型的格式要求。
* expr的值不能超过目标类型的值域，如10000无法转换为TINYINT类型。
* expr的值为NULL时，函数返回NULL。
* 当目标类型为BOOLEAN时，表现和将数据插入BOOLEAN表列后查询的结果相同。
* 当NUMBER转换BIT时，函数将对NUMBER进行向下取整，且NUMBER数据的大小不能超过64位。
* 当TIME转换DATE跟TIMESTAMPS时，取当天的日期来补齐年月日。
* 产生的字符串本身不带字符集属性，所以当用此字符串再进入数据库中作为输入时，使用数据库默认字符集进行解析，可能会产生不可预期的错误。

**type_name**

* 当目标类型为字符型数据时可以指定其size，若expr为字符串类型且长度大于指定size则会截断，若expr为其他类型且长度大于指定size则转换失败并返回Out of range错误。
* 当目标类型为CHAR或NCHAR且未指定其size时，无需关注expr的数据类型，其预估size长度默认为1。
* 当目标类型为VARCHAR且未指定其size时，将根据expr的类型对size进行预估。对于字符串类型，其预估size为原字符串长度；对于其他类型，其预估size为该类型转为字符串的长度。
* 当目标类型为NVARCHAR时，必须指定其size，否则返回错误。

**charset_info**

仅当type_name为CHAR或VARCHAR时，才支持此语法。在没有charset_info子句的情况下，CHAR或VARCHAR使用默认字符集生成字符串。如需明确指定字符集，可以使用以下charset_info值：

* CHARACTER SET charset_name：使用给定的字符集生成字符串，其中charset_name支持的范围与mysql模式下所支持的字符集范围相同。

* ASCII：CHARACTER SET latin1的简称。

**transcoding_name**

指定要转换的目标字符集，支持的字符集范围与mysql模式下所支持的字符集范围相同。

示例（单机HEAP表）

```sql
SELECT CONVERT('abcd' ,CHAR(6)) res;

res  
------------------------- 
abcd                     

SELECT HEX(CONVERT('——' ,CHAR(9) ascii)) res;
res
----------------------------------
2D2D2D2D                                   

SELECT HEX(CONVERT('——' USING utf8)) res;
res
------------------------------
E28094E28094
                     
SELECT HEX(CONVERT('——' ,CHAR(9) CHARACTER SET gbk)) res;
res
----------------------------------------------
A1AAA1AA         
```
