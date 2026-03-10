本文将介绍在setObejct()方法中JDBC数据类型和Java数据类型的映射关系。

(1)

| JDBC类型<br/>\\Java类型 | boolean | String | byte,short<br/>,int,long | float,<br/>double | Big<br/>Decimal |
| --- | --- | --- | --- | --- | --- |
| BIT | √ 注3 | √   | √注3 | ×   | √   |
| BOOLEAN | √   | √注1 | √ 注2 | ×   | ×   |
| CHAR,VARCHAR,NCHAR,NVARCHAR | √   | √   | √   | √   | √   |
| DATE | ×   | √   | ×   | ×   | ×   |
| TIMESTAMP | ×   | √   | ×   | ×   | ×   |
|  TIME |  ×  |  √  |  ×  |  ×  |  ×  |
| TINYINT, SMALLINT,<br/>INTEGER,BIGINT | √   | √   | √   | √   | √   |
| REAL (Note 4)<br/>(Server: FLOAT ) | √   | √   | √   | √   | √   |
| FLOAT,<br/>DOUBLE (Note 4)<br/>(Server: DOUBLE) |  √  |  √  |  √  |  √  | √   |
| DECIMAL,<br/>NUMERIC(Note 4)<br/>(Server: NUMBER) | √   | √   | √   | √   | √   |

(2)

| JDBC类型<br/>\\Java类型 | Time  |  Date | Timestamp | byte\[\] | Local<br/>Time | Local<br/>DateTime | Local<br/>Date |
| --- | --- | --- | --- | --- | --- | --- | --- |
| BIT |  ×  | ×   | ×   | √   | ×   | ×   | ×   |
| BOOLEAN |  ×  | ×   | ×   | ×   | ×   | ×   | ×   |
| CHAR,VARCHAR,NCHAR,NVARCHAR |  √  | √   | √   | ×   | ×   | ×   | ×   |
| DATE |  √  | √   | √   | ×   | ×   | ×   | √   |
| TIMESTAMP |  √  | √   | √   | ×   | ×   | √   | ×   |
|  TIME |  √  |  √  |  √  |  ×  |  √  | ×   | ×   |
| TINYINT, SMALLINT,<br/>INTEGER,BIGINT | ×   | ×   | ×   | ×   | ×   | ×   | ×   |
| REAL (Note 4)<br/>(Server: FLOAT ) | ×   | ×   | ×   | ×   | ×   | ×   | ×   |
| FLOAT,<br/>DOUBLE (Note 4)<br/>(Server: DOUBLE) | ×   | ×   | ×   | ×   | ×   | ×   | ×   |
| DECIMAL,<br/>NUMERIC(Note 4)<br/>(Server: NUMBER) | ×   | ×   | ×   | ×   | ×   | ×   | ×   |

注1：

BOOLEAN字符串形式支持：

- 'TRUE'/'FALSE'
- 'ON'/'OFF'
- 'YES'/'NO'
- 'T'/'F'
- 转数值的字符'1'/'0'

注2：

BOOLEAN整型数值形式支持非0转换为TRUE，0转换为FALSE。

注3：

BIT类型可以转换为整型，但有溢出判定；其与BOOLEAN转换时，1转换为TRUE，全0转换为FALSE，其他数值报溢出。

注4：（仅用于setObject）

表示数据库定义类型与JDBC定义类型映射名称不一样。