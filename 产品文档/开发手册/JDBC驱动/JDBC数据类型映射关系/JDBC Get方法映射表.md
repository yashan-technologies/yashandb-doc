(1)

| **YashanDB Data Types/JDBC** | get<br>String | get<br/>Object | get<br/>Boolean | get<br/>Int | get<br/>Byte | get<br/>Short | get<br/>Long |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | Y   | Y   | Y | Y | Y | Y | Y |
| VARCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| NCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| NVARCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| BOOLEAN | Y   | Y   | Y   | Y | Y | Y | Y |
| TINYINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| SMALLINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| INTEGER | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| BIGINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| FLOAT | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| DOUBLE | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| NUMBER | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| BIT | Y | Y | Y | Y | Y | Y | Y |

(2)

| **YashanDB Data Types/JDBC** | get<br/>Bytes | get<br/>Float | get<br/>Double | getBig<br/>Decimal | get<br/>Date | get<br/>Time | get<br/>Timestamp |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | Y | Y | Y | Y | Y | Y | Y |
| VARCHAR | Y | Y | Y | Y | Y | Y | Y |
| NCHAR | Y | Y | Y | Y | Y | Y | Y |
| NVARCHAR | Y | Y | Y | Y | Y | Y | Y |
| BOOLEAN | Y | Y | Y | Y | N | N | N |
| TINYINT | Y   | Y   | Y   | Y   | N | N | N |
| SMALLINT | Y   | Y   | Y   | Y   | N | N | N |
| INTEGER | Y   | Y   | Y   | Y   | N | N | N |
| BIGINT | Y   | Y   | Y   | Y   | N | N | N |
| FLOAT | Y   | Y   | Y   | Y   | N | N | N |
| DOUBLE | Y   | Y   | Y   | Y   | N | N | N |
| NUMBER | Y   | Y   | Y   | Y   | N | N | N |
| BIT | Y | Y | Y | Y | N | N | N |

(3)

| **YashanDB Data Types/JDBC** | get<br/>String | get<br/>Object | get<br/>Boolean | get<br/>Int | get<br/>Byte | get<br/>Short | get<br/>Long |
|------------------------------| --- | --- | --- | --- | --- | --- | --- |
| RAW                          | Y   | Y   | N | N | N | N | N |
| DATE                         | Y   | Y   | N | N | N | N | N |
| TIMESTAMP                    | Y   | Y   | N | N | N | N | N |
| TIME                         | Y   | Y   | N | N | N | N | N |
| BLOB                         | Y   | Y   | N | N | N | N | N |
| CLOB                         | Y   | Y   | N | N | N | N | N |
| NCLOB                        | Y   | Y   | N | N | N | N | N |
| OBJECT                       | N   | N   | N | N | N | N | N |
| REF                          | N   | N   | N | N | N | N | N |
| ARRAY                        | N   | N   | N | N | N | N | N |
| ROWID                        | Y   | Y   | N | N | N | N | N |
| INTERVAL DAY TO SECOND       | Y   | Y   | N | N | N | N | N |
| INTERVAL YEAR TO MONTH       | Y   | Y   | N | N | N | N | N |
| JSON                         | Y | Y | N | N | N | N | N |
| XMLTYPE                      | Y | Y | N | N | N | N | N |

(4)

| **YashanDB Data Types/JDBC** | get<br/>Bytes | get<br/>Float | get<br/>Double | getBig<br/>Decimal | get<br/>Date | get<br/>Time | get<br/>Timestamp |
|------------------------------|---------------| --- | --- | --- | --- | --- | --- |
| RAW                          | Y             | N | N | N | N | N | N |
| DATE                         | Y             | N | N | N | Y   | Y   | Y |
| TIMESTAMP                    | Y             | N | N | N | Y   | Y | Y   |
| TIME                         | Y             | N | N | N | Y   | Y | Y   |
| BLOB                         | Y             | N | N | N | N | N | N |
| CLOB                         | N             | N | N | N | N | N | N |
| NCLOB                        | N             | N | N | N | N | N | N |
| OBJECT                       | N             | N | N | N | N | N | N |
| REF                          | N             | N | N | N | N | N | N |
| ARRAY                        | N             | N | N | N | N | N | N |
| ROWID                        | Y             | N | N | N | N | N | N |
| INTERVAL DAY TO SECOND       | Y             | N | N | N | N | N | N |
| INTERVAL YEAR TO MONTH       | Y             | N | N | N | N | N | N |
| JSON                         | Y             | N | N | N | N | N | N |
| XMLTYPE                      | Y             | N | N | N | N | N | N |

(5)

| **YashanDB Data Types/JDBC** | get<br/>Blob | get<br/>Clob | get<br/>NClob | getAscii Stream | get<br/>Character Stream | get<br/>Unicode Stream |
| --- | --- | --- | --- | --- | --- | --- |
| CHAR | N | N | N | Y | Y | N |
| VARCHAR | N | N | N | Y | Y | N |
| NCHAR | N | N | N | Y | Y | N |
| NVARCHAR | N | N | N | Y | Y | N |
| BOOLEAN | N | N | N | N | N | N |
| TINYINT | N | N | N | N | N | N |
| SMALLINT | N | N | N | N | N | N |
| INTEGER | N | N | N | N | N | N |
| BIGINT | N | N | N | N | N | N |
| FLOAT | N | N | N | N | N | N |
| DOUBLE | N | N | N | N | N | N |
| NUMBER | N | N | N | N | N | N |
| BIT | N | N | N | N | N | N |

(6)

| **YashanDB Data Types/JDBC** | get<br/>Binary Stream | get<br/>RowId | get<br/>NCharacter Stream | get<br/>NString | get<br/>Array | get<br/>SQLXML | get<br/>Ref | get<br/>URL |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | Y | Y | N | Y | N | N | N | N |
| VARCHAR | Y | Y | N | Y | N | N | N | N |
| NCHAR | Y | Y | N | Y | N | N | N | N |
| NVARCHAR | Y | Y | N | Y | N | N | N | N |
| BOOLEAN | N | N | N | Y | N | N | N | N |
| TINYINT | N | N | N | Y | N | N | N | N |
| SMALLINT | N | N | N | Y | N | N | N | N |
| INTEGER | N | N | N | Y | N | N | N | N |
| BIGINT | N | N | N | Y | N | N | N | N |
| FLOAT | N | N | N | Y | N | N | N | N |
| DOUBLE | N | N | N | Y | N | N | N | N |
| NUMBER | N | N | N | Y | N | N | N | N |
| BIT | N | N | N | Y | N | N | N | N |

(7)

| **YashanDB Data Types/JDBC** | get<br/>Blob | get<br/>Clob | get<br/>NClob | getAscii Stream | get<br/>Character Stream | get<br/>Unicode Stream |
|------------------------------|--------------|--------------| --- | --- | --- | --- |
| RAW                          | N            | N            | N | N | N | N |
| DATE                         | N            | N            | N | N | N | N |
| TIMESTAMP                    | N            | N            | N | N | N | N |
| TIME                         | N            | N            | N | N | N | N |
| BLOB                         | Y            | N            | N | N | N | N |
| CLOB                         | N            | Y            | N | N | N | N |
| NCLOB                        | N            | Y            | N | N | N | N |
| OBJECT                       | N            | N            | N | N | N | N |
| REF                          | N            | N            | N | N | N | N |
| ARRAY                        | N            | N            | N | N | N | N |
| ROWID                        | N            | N            | N | N | N | N |
| INTERVAL DAY TO SECOND       | N            | N            | N | N | N | N |
| INTERVAL YEAR TO MONTH       | N            | N            | N | N | N | N |
| JSON                         | Y            | N            | N | N | N | N |
| XMLTYPE                      | N            | Y            | N | N | N | N |

(8)

| **YashanDB Data Types/JDBC** | get<br/>Binary Stream | get<br/>RowId | get<br/>NCharacter Stream | get<br/>NString | get<br/>Array | get<br/>SQLXML | get<br/>Ref | get<br/>URL |
| --- | --- | --- | --- | --- | --- |----------------| --- | --- |
| RAW | N | N | N | Y | N | N              | N | N |
| DATE | N | N | N | Y | N | N              | N | N |
| TIMESTAMP | N | N | N | Y | N | N              | N | N |
| TIME | N | N | N | Y | N | N              | N | N |
| BLOB | Y   | N | N | Y | N | N              | N | N |
| CLOB | N | N | N | Y | N | N              | N | N |
| NCLOB | N | N | Y | Y | N | N              | N | N |
| OBJECT | N | N | N | N | N | N              | N | N |
| REF | N | N | N | N | N | N              | N | N |
| ARRAY | N | N | N | N | Y   | N              | N | N |
| ROWID | N | Y   | N | Y | N | N              | N | N |
| INTERVAL DAY TO SECOND | N | N | N | Y | N | N              | N | N |
| INTERVAL YEAR TO MONTH | N | N | N | Y | N | N              | N | N |
| JSON | N | N | N | Y | N | N              | N | N |
| XMLTYPE | N | N | N | Y | N | Y              | N | N |