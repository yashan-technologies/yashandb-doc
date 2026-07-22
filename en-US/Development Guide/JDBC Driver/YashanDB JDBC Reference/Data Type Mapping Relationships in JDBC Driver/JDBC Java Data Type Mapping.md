This article will introduce the mapping between JDBC data types and Java data types in the setObject() method.

|JDBC Type<br/>\\Java Type |boolean |String |byte, short<br/>, int, long |float,<br/>double |Big<br/>Decimal |
| --- | --- | --- | --- | --- | --- |
| BIT | √ Note 3 | √   | √ Note 3 | ×   | √   |
| BOOLEAN | √   | √ Note 1 | √ Note 2 | ×   | ×   |
| CHAR,VARCHAR,NCHAR,NVARCHAR | √   | √   | √   | √   | √   |
| DATE | ×   | √   | ×   | ×   | ×   |
| TIMESTAMP | ×   | √   | ×   | ×   | ×   |
|  TIME |  ×  |  √  |  ×  |  ×  |  ×  |
| TINYINT, SMALLINT,<br/>INTEGER,BIGINT | √   | √   | √   | √   | √   |
| REAL (Note 4)<br/>(Server: FLOAT ) | √   | √   | √   | √   | √   |
| FLOAT,<br/>DOUBLE (Note 4)<br/>(Server: DOUBLE) |  √  |  √  |  √  |  √  | √   |
| DECIMAL,<br/>NUMERIC(Note 4)<br/>(Server: NUMBER) | √   | √   | √   | √   | √   |



|JDBC Type<br/>\\Java Type |Time |Date |Timestamp |byte\[\] |Local<br/>Time |Local<br/>DateTime |Local<br/>Date |
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

Note 1:

The string format of BOOLEAN supports:

- 'TRUE'/'FALSE'
- 'ON'/'OFF'
- 'YES'/'NO'
- 'T'/'F'
- Numeric characters '1'/'0' that convert to values

Note 2:

The integer format of BOOLEAN supports non-zero value conversion to TRUE, zero conversion to FALSE.

Note 3:

BIT type can be converted to integer, but overflow is checked; when converting to BOOLEAN, 1 converts to TRUE, all 0 converts to FALSE, other values report overflow.

Note 4: (Only used for setObject)

Indicates that the mapping names of database-defined types and JDBC-defined types are different.