> **Note**:
>
> - mysql模式数据类型特指YashanDB运行于mysql模式时的SQL数据类型。
> - 当USE_NATIVE_TYPE参数设置为FALSE时，YashanDB将采用Oracle兼容数据类型（mysql模式下忽略此设定，不会采用Oracle兼容数据类型），依据兼容类型在下表进行对照，例如NUMBER和FLOAT。

(1)

| **YashanDB Data Types** | **mysql mode Data Types** | get<br>String | get<br/>Object | get<br/>Boolean | get<br/>Int | get<br/>Byte | get<br/>Short | get<br/>Long |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y   | Y   | Y | Y | Y | Y | Y |
| VARCHAR | VARCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| NCHAR | NCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| NVARCHAR | NVARCHAR | Y   | Y   | Y | Y | Y | Y | Y |
| BOOLEAN | BOOLEAN | Y   | Y   | Y   | Y | Y | Y | Y |
| TINYINT | TINYINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| SMALLINT | SMALLINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| INTEGER | INTEGER | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| BIGINT | BIGINT | Y   | Y   | Y   | Y   | Y   | Y   | Y   |
| FLOAT | FLOAT | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| DOUBLE | DOUBLE | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| NUMBER | NUMBER | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| BIT | BIT | Y | Y | Y | Y | Y | Y | Y |

(2)

| **YashanDB Data Types** | **mysql mode Data Types** | get<br/>Bytes | get<br/>Float | get<br/>Double | getBig<br/>Decimal | get<br/>Date | get<br/>Time | get<br/>Timestamp |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y | Y | Y | Y | Y | Y | Y |
| VARCHAR | VARCHAR | Y | Y | Y | Y | Y | Y | Y |
| NCHAR | NCHAR | Y | Y | Y | Y | Y | Y | Y |
| NVARCHAR | NVARCHAR | Y | Y | Y | Y | Y | Y | Y |
| BOOLEAN | BOOLEAN | Y | Y | Y | Y | N | N | N |
| TINYINT | TINYINT | Y   | Y   | Y   | Y   | N | N | N |
| SMALLINT | SMALLINT | Y   | Y   | Y   | Y   | N | N | N |
| INTEGER | INTEGER | Y   | Y   | Y   | Y   | N | N | N |
| BIGINT | BIGINT | Y   | Y   | Y   | Y   | N | N | N |
| FLOAT | FLOAT | Y   | Y   | Y   | Y   | N | N | N |
| DOUBLE | DOUBLE | Y   | Y   | Y   | Y   | N | N | N |
| NUMBER | NUMBER | Y   | Y   | Y   | Y   | N | N | N |
| BIT | BIT | Y | Y | Y | Y | N | N | N |

(3)

| **YashanDB Data Types** | **mysql mode Data Types**         | get<br/>String | get<br/>Object | get<br/>Boolean | get<br/>Int | get<br/>Byte | get<br/>Short | get<br/>Long |
|------------------------------|-----------------------------------| --- | --- | --- | --- | --- | --- | --- |
| RAW                          | BINARY/VARBINARY                  | Y   | Y   | N | N | N | N | N |
| DATE                         | DATE                              | Y   | Y   | N | N | N | N | N |
| TIMESTAMP                    | TIMESTAMP                         | Y   | Y   | N | N | N | N | N |
| TIME                         | TIME                              | Y   | Y   | N | N | N | N | N |
| BLOB                         | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | Y   | Y   | N | N | N | N | N |
| CLOB                         | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y   | Y   | N | N | N | N | N |
| NCLOB                        | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y   | Y   | N | N | N | N | N |
| OBJECT                       | OBJECT                            | N   | N   | N | N | N | N | N |
| REF                          | REF                               | N   | N   | N | N | N | N | N |
| ARRAY                        | ARRAY                             | N   | N   | N | N | N | N | N |
| ROWID                        | ROWID                             | Y   | Y   | N | N | N | N | N |
| INTERVAL DAY TO SECOND       | INTERVAL DAY TO SECOND            | Y   | Y   | N | N | N | N | N |
| INTERVAL YEAR TO MONTH       | INTERVAL YEAR TO MONTH            | Y   | Y   | N | N | N | N | N |
| JSON                         | JSON                              | Y | Y | N | N | N | N | N |

(4)

| **YashanDB Data Types** | **mysql mode Data Types**         | get<br/>Bytes | get<br/>Float | get<br/>Double | getBig<br/>Decimal | get<br/>Date | get<br/>Time | get<br/>Timestamp |
|------------------------------|-----------------------------------|---------------| --- | --- | --- | --- | --- | --- |
| RAW                          | BINARY/VARBINARY                  | Y             | N | N | N | N | N | N |
| DATE                         | DATE                              | Y             | N | N | N | Y   | Y   | Y |
| TIMESTAMP                    | TIMESTAMP                         | Y             | N | N | N | Y   | Y | Y   |
| TIME                         | TIME                              | Y             | N | N | N | Y   | Y | Y   |
| BLOB                         | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | Y             | N | N | N | N | N | N |
| CLOB                         | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N             | N | N | N | N | N | N |
| NCLOB                        | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N             | N | N | N | N | N | N |
| OBJECT                       | OBJECT                            | N             | N | N | N | N | N | N |
| REF                          | REF                               | N             | N | N | N | N | N | N |
| ARRAY                        | ARRAY                             | N             | N | N | N | N | N | N |
| ROWID                        | ROWID                             | Y             | N | N | N | N | N | N |
| INTERVAL DAY TO SECOND       | INTERVAL DAY TO SECOND            | Y             | N | N | N | N | N | N |
| INTERVAL YEAR TO MONTH       | INTERVAL YEAR TO MONTH            | Y             | N | N | N | N | N | N |
| JSON                         | JSON                              | Y             | N | N | N | N | N | N |

(5)

| **YashanDB Data Types** | **mysql mode Data Types** | get<br/>Blob | get<br/>Clob | get<br/>NClob | getAscii Stream | get<br/>Character Stream | get<br/>Unicode Stream |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | N | N | N | Y | Y | N |
| VARCHAR | VARCHAR | N | N | N | Y | Y | N |
| NCHAR | NCHAR | N | N | N | Y | Y | N |
| NVARCHAR | NVARCHAR | N | N | N | Y | Y | N |
| BOOLEAN | BOOLEAN | N | N | N | N | N | N |
| TINYINT | TINYINT | N | N | N | N | N | N |
| SMALLINT | SMALLINT | N | N | N | N | N | N |
| INTEGER | INTEGER | N | N | N | N | N | N |
| BIGINT | BIGINT | N | N | N | N | N | N |
| FLOAT | FLOAT | N | N | N | N | N | N |
| DOUBLE | DOUBLE | N | N | N | N | N | N |
| NUMBER | NUMBER | N | N | N | N | N | N |
| BIT | BIT | N | N | N | N | N | N |

(6)

| **YashanDB Data Types** | **mysql mode Data Types** | get<br/>Binary Stream | get<br/>RowId | get<br/>NCharacter Stream | get<br/>NString | get<br/>Array | get<br/>SQLXML | get<br/>Ref | get<br/>URL |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y | Y | N | Y | N | N | N | N |
| VARCHAR | VARCHAR | Y | Y | N | Y | N | N | N | N |
| NCHAR | NCHAR | Y | Y | N | Y | N | N | N | N |
| NVARCHAR | NVARCHAR | Y | Y | N | Y | N | N | N | N |
| BOOLEAN | BOOLEAN | N | N | N | Y | N | N | N | N |
| TINYINT | TINYINT | N | N | N | Y | N | N | N | N |
| SMALLINT | SMALLINT | N | N | N | Y | N | N | N | N |
| INTEGER | INTEGER | N | N | N | Y | N | N | N | N |
| BIGINT | BIGINT | N | N | N | Y | N | N | N | N |
| FLOAT | FLOAT | N | N | N | Y | N | N | N | N |
| DOUBLE | DOUBLE | N | N | N | Y | N | N | N | N |
| NUMBER | NUMBER | N | N | N | Y | N | N | N | N |
| BIT | BIT | N | N | N | Y | N | N | N | N |

(7)

| **YashanDB Data Types**  | **mysql mode Data Types**         | get<br/>Blob | get<br/>Clob | get<br/>NClob | getAscii Stream | get<br/>Character Stream | get<br/>Unicode Stream |
|--------------------------|-----------------------------------|--------------|--------------| --- | --- | --- | --- |
| RAW                      | BINARY/VARBINARY                  | N            | N            | N | N | N | N |
| DATE                     | DATE                              | N            | N            | N | N | N | N |
| TIMESTAMP                | TIMESTAMP                         | N            | N            | N | N | N | N |
| TIME                     | TIME                              | N            | N            | N | N | N | N |
| BLOB                     | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | Y            | N            | N | N | N | N |
| CLOB                     | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N            | Y            | N | N | N | N |
| NCLOB                    | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N            | Y            | N | N | N | N |
| OBJECT                   | OBJECT                            | N            | N            | N | N | N | N |
| REF                      | REF                               | N            | N            | N | N | N | N |
| ARRAY                    | ARRAY                             | N            | N            | N | N | N | N |
| ROWID                    | ROWID                             | N            | N            | N | N | N | N |
| INTERVAL DAY TO SECOND   | INTERVAL DAY TO SECOND            | N            | N            | N | N | N | N |
| INTERVAL YEAR TO MONTH   | INTERVAL YEAR TO MONTH            | N            | N            | N | N | N | N |
| JSON                     | JSON                              | Y            | N            | N | N | N | N |

(8)

| **YashanDB Data Types**  | **mysql mode Data Types**         | get<br/>Binary Stream | get<br/>RowId | get<br/>NCharacter Stream | get<br/>NString | get<br/>Array | get<br/>SQLXML | get<br/>Ref | get<br/>URL |
|--------------------------|-----------------------------------| --- | --- | --- | --- | --- |----------------| --- | --- |
| RAW                      | BINARY/VARBINARY                  | N | N | N | Y | N | N              | N | N |
| DATE                     | DATE                              | N | N | N | Y | N | N              | N | N |
| TIMESTAMP                | TIMESTAMP                         | N | N | N | Y | N | N              | N | N |
| TIME                     | TIME                              | N | N | N | Y | N | N              | N | N |
| BLOB                     | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | Y   | N | N | Y | N | N              | N | N |
| CLOB                     | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N | N | N | Y | N | N              | N | N |
| NCLOB                    | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | N | N | Y | Y | N | N              | N | N |
| OBJECT                   | OBJECT                            | N | N | N | N | N | N              | N | N |
| REF                      | REF                               | N | N | N | N | N | N              | N | N |
| ARRAY                    | ARRAY                             | N | N | N | N | Y   | N              | N | N |
| ROWID                    | ROWID                             | N | Y   | N | Y | N | N              | N | N |
| INTERVAL DAY TO SECOND   | INTERVAL DAY TO SECOND            | N | N | N | Y | N | N              | N | N |
| INTERVAL YEAR TO MONTH   | INTERVAL YEAR TO MONTH            | N | N | N | Y | N | N              | N | N |
| JSON                     | JSON                              | N | N | N | Y | N | N              | N | N |
