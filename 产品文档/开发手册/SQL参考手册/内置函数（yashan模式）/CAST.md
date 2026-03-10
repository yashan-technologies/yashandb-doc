```ebnf+diagram
cast::= CAST "("expr AS type_name [DEFAULT replace_expr ON CONVERSION ERROR] ")"
```

CAST函数将[expr](../通用SQL语法/expr)的值转换为指定的数据类型，并按新的类型返回结果。

YashanDB支持如下情况的类型转换：

| expr类型 | type_name                                                             |
| --- |------------------------------------------------------------------------|
| BOOLEAN | TINYINT、SMALLINT、INT、BIGINT、CHAR、VARCHAR、NCHAR、NVARCHAR                |
| BIT | 除FLOAT、DOUBLE外的数值型、字符型数据                                               |
| CHAR、VARCHAR | 除UDT外的所有数据类型                                                           |
| NCHAR、NVARCHAR | 除UDT、XMLTYPE外的所有数据类型                                                   |
| FLOAT、DOUBLE | 除BIT外的数值型、字符型数据                                                        |
| NUMBER | 所有数值型、字符型                                                              |
| TINYINT、SMALLINT、INT、BIGINT | 所有数值型、字符型                                                              |
| TIME | 字符型数据、DATE、TIMESTAMP、TIMESTAMP TZ、TIMESTAMP LTZ、INTERVAL DAY TO SECOND |
| DATE | 字符型数据、TIMESTAMP、TIMESTAMP TZ、TIMESTAMP LTZ、TIME                        |
| TIMESTAMP | 字符型数据、DATE、TIMESTAMP TZ、TIMESTAMP LTZ、TIME                             |
| TIMESTAMP WITH LOCAL TIME  | 字符型数据、DATE、TIMESTAMP、TIMESTAMP TZ、TIME                             |
| TIMESTAMP WITH TIME ZONE| 字符型数据、DATE、TIMESTAMP、TIMESTAMP LTZ、TIME                             |
| INTERVAL YEAR TO MONTH | 字符型数据                                                                  |
| INTERVAL DAY TO SECOND | 字符型数据                                                                  |
| CLOB | 字符型数据、JSON、XMLTYPE                                                     |
| NCLOB | 字符型数据、JSON                                                             |
| BLOB | 字符型数据、JSON、RAW、UROWID                                                  |
| XMLTYPE | CHAR、VARCHAR                                                           |
| ROWID | 字符型数据、RAW、UROWID                                                       |
| UROWID | 字符型数据、RAW                                                              |
| JSON | 字符型数据、CLOB、BLOB、NCLOB、RAW                                              |
| RAW | 字符型数据、CLOB、BLOB、NCLOB、UROWID                                           |

其中：

* expr的内容需符合目标类型的格式要求。
* expr的值不能超过目标类型的值域，如10000无法转换为TINYINT类型。
* expr的值为NULL时，函数返回NULL。
* 对于列存表中的LOB类型字段，若某行数据为行外存储，则无法使用本函数进行转换。
* 当目标类型为BOOLEAN时，只有'TRUE'、'T'、'YES'、'Y'、'1'可转换为TRUE，'FALSE'、'F'、'NO、'N'、'0'可转换为FALSE，字符不分大小写。
* 当NUMBER转换BIT时，函数将对NUMBER进行向下取整，且NUMBER数据的大小不能超过64位。
* 当TIME转其余时间类型时，取当天的日期来补齐年月日。

**type_name**

指定要转换的目标类型，可同时指定size、scale、precision。   

若目标类型为字符型数据时，可以指定其size，若expr为字符串类型且长度大于指定的size会截断，为其他类型超过指定的size，则转换失败并返回Out of range错误。  

若目标类型为CHAR或NCHAR时，且语句中未指定其size时，不需要关注需要转换的原始数据类型，其预估size长度默认为1。

若目标类型为VARCHAR时，且语句中未指定其size，将根据expr的类型对size进行预估。对于字符串类型，其预估size为原字符串长度；对于其他类型，其预估size为该类型转为字符串的长度。  

若目标类型为NVARCHAR时，须在语句中指定其size，否则返回错误。  

**DEFAULT replace_expr ON CONVERSION ERROR**

表示当对expr转换失败时，使用replace_expr值来进行转换。 replace_expr可以为NULL，字符串值和字面量，且必须能够转换为目标类型（转换类型支持，且转换内容符合格式要求）。

此语句可省略，则对expr转换失败时函数返回错误。

当expr的数据类型为BIT、LOB类型，或者type_name为BIT、LOB类型时，不允许指定此语句。

  

示例

```sql
SELECT CAST('345' AS FLOAT) cast1,
CAST('345.2345' AS NUMBER(6,2)) cast2,
CAST('345a' AS INT DEFAULT '' ON CONVERSION ERROR) cast3,
CAST(SYSTIMESTAMP AS CHAR(100)) cast4
FROM DUAL;
      CAST1     CAST2    CAST3 CAST4                     
----------- --------- -------- ---------------------------
  3.45E+002    345.23          2022-01-09 17:52:30.634370

SELECT CAST('abcdef' AS CHAR) cast1,
CAST(1.23456 AS VARCHAR) cast2,
CAST(NULL AS CHAR) cast3,
CAST(NULL AS VARCHAR) cast4
FROM DUAL;
CAST1 CAST2     CAST3 CAST4 
----- --------- ----- ----- 
a     1.23456     
```

