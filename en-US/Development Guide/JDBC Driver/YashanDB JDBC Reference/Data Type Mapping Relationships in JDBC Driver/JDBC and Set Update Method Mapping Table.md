This article will introduce the mapping between JDBC data types and Set Update data types in the setObject() method.

> **Note**:
>
> - The mysql mode data types specifically refer to SQL data types when YashanDB is running in mysql mode. 
> - When the USE_NATIVE_TYPE parameter is set to FALSE, YashanDB will adopt Oracle-compatible data types (this setting is ignored in MySQL mode, and Oracle-compatible data types will not be adopted). According to the compatibility types, refer to the table below for mapping, such as NUMBER and FLOAT. 



| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>String | set<br/>Object | set<br/>Null | set<br/>Boolean | set<br/>Int | set<br/>Byte | set<br/>Short | set<br/>Long |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| VARCHAR | VARCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| NCHAR | NCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| NVARCHAR | NVARCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| BOOLEAN | BOOLEAN | Y   | Y   | Y   | Y   | Y | Y | Y | Y |
| TINYINT | TINYINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| SMALLINT | SMALLINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| INTEGER | INTEGER | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| BIGINT | BIGINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| FLOAT | FLOAT | Y   | Y   | Y   | N | Y   | Y   | Y   | Y   |
| DOUBLE | DOUBLE | Y   | Y   | Y   | N | Y   | Y   | Y   | Y   |
| NUMBER | NUMBER | Y | Y   | Y   | Y | Y   | Y   | Y   | Y   |

(2)

| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>Bytes | set<br/>Float | set<br/>Double | set<br/>Big<br>Decimal | set<br/>Date | set<br/>Time | set<br/>Timestamp | set<br/>Blob |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| VARCHAR | VARCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| NCHAR | NCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| NVARCHAR | NVARCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| BOOLEAN | BOOLEAN | Y | N | N | Y | N | N | N | N |
| TINYINT | TINYINT | N   | Y   | Y   | Y | N | N | N | N |
| SMALLINT | SMALLINT | N   | Y   | Y   | Y | N | N | N | N |
| INTEGER | INTEGER | N   | Y   | Y   | Y | N | N | N | N |
| BIGINT | BIGINT | N   | Y   | Y   | Y | N | N | N | N |
| FLOAT | FLOAT | N   | Y   | Y   | Y | N | N | N | N |
| DOUBLE | DOUBLE | N   | Y   | Y   | Y | N | N | N | N |
| NUMBER | NUMBER | N   | Y   | Y   | Y   | N | N | N | N |

(3)

| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>Clob | set<br/>NClob | set<br/>Ascii<br>Stream | set<br/>Character<br>Stream | set<br/>Unicode<br>Stream |
| --- | --- | --- | --- | --- | --- | --- |
| CHAR | CHAR | Y | N | Y | Y | N |
| VARCHAR | VARCHAR | Y | N | Y | Y | N |
| NCHAR | NCHAR | Y | N | Y | Y | N |
| NVARCHAR | NVARCHAR | Y | N | Y | Y | N |
| BOOLEAN | BOOLEAN | N | N | N | Y | N |
| TINYINT | TINYINT | N | N | N | Y | N |
| SMALLINT | SMALLINT | N | N | N | Y | N |
| INTEGER | INTEGER | N | N | N | Y | N |
| BIGINT | BIGINT | N | N | N | Y | N |
| FLOAT | FLOAT | N | N | N | Y | N |
| DOUBLE | DOUBLE | N | N | N | Y | N |
| NUMBER | NUMBER | N | N | N | Y | N |

(4)

| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>Binary<br>Stream | set<br/>RowId | set<br/>NCharacter<br>Stream | set<br/>NString | set<br/>Array | set<br/>SQL<br/>XML | set<br/>Ref | set<br/>URL |
| --- | --- | --- | --- | --- | --- | --- |---| --- | --- |
| CHAR | CHAR | Y | Y | N | Y | N | Y | N | N |
| VARCHAR | VARCHAR | Y | Y | N | Y | N | Y | N | N |
| NCHAR | NCHAR | Y | Y | N | Y | N | Y | N | N |
| NVARCHAR | NVARCHAR | Y | Y | N | Y   | N | Y | N | N |
| BOOLEAN | BOOLEAN | N | N | N | Y | N | N | N | N |
| TINYINT | TINYINT | N | N | N | Y | N | N | N | N |
| SMALLINT | SMALLINT | N | N | N | Y | N | N | N | N |
| INTEGER | INTEGER | N | N | N | Y | N | N | N | N |
| BIGINT | BIGINT | N | N | N | Y | N | N | N | N |
| FLOAT | FLOAT | N | N | N | Y | N | N | N | N |
| DOUBLE | DOUBLE | N | N | N | Y | N | N | N | N |
| NUMBER | NUMBER | N | N | N | Y | N | N | N | N |

(5)

| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>String | set<br/>Object | set<br/>Null | set<br/>Boolean | set<br/>Int | set<br/>Byte | set<br/>Short | set<br/>Long |
|------------------------|------------------------| --- | --- | --- | --- | --- | --- | --- | --- |
| RAW                    | BINARY/VARBINARY                          | Y | Y   | Y   | N | N | N | N | N |
| DATE                   | DATE                         | Y | Y   | Y   | N | N | N | N | N |
| TIMESTAMP              | TIMESTAMP                    | Y | Y   | Y   | N | N | N | N | N |
| TIME                   | TIME                         | Y | Y   | Y   | N | N | N | N | N |
| BLOB                   |BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB                        | Y | Y   | Y   | N | N | N | N | N |
| CLOB                   |TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT                         | Y | Y   | Y   | N | Y | Y | Y | Y |
| NCLOB                  |TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT                         | Y | Y   | Y   | N | Y | Y | Y | Y |
| OBJECT                 | OBJECT                 | N | N   | N   | N | N | N | N | N |
| REF                    | REF                    | N | N   | N   | N | N | N | N | N |
| ARRAY                  | ARRAY                  | N | N   | N   | N | N | N | N | N |
| ROWID                  | ROWID                  | Y | Y   | Y   | N | N | N | N | N |
| UROWID                 | UROWID                 | Y | Y   | Y   | N | N | N | N | N |
| INTERVAL DAY TO SECOND | INTERVAL DAY TO SECOND | Y | Y   | Y   | N | N | N | N | N |
| INTERVAL YEAR TO MONTH | INTERVAL YEAR TO MONTH | Y | Y   | Y   | N | N | N | N | N |
| BIT                    | BIT                    | Y | Y | Y | Y | Y | Y | Y | Y |
| JSON                   | JSON                   | Y | Y | Y | N | N | N | N | N |

(6)

| **YashanDB Data Types** | **mysql mode Data Types**         | set<br/>Bytes | set<br/>Float | set<br/>Double | setBig<br/>Decimal | set<br/>Date | set<br/>Time | set<br/>Timestamp | set<br/>Blob |
|------------------------|-----------------------------------|---------------| --- | --- | --- | --- | --- | --- |--------------|
| RAW                    | BINARY/VARBINARY                  | Y             | N | N | N | N | N | N | Y            |
| DATE                   | DATE                              | N             | N | N | N | Y   | Y   | Y | N            |
| TIMESTAMP              | TIMESTAMP                         | N             | N | N | N | Y   | Y | Y   | N            |
| TIME                   | TIME                              | N             | N | N | N | Y   | Y | Y   | N            |
| BLOB                   | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | Y             | N | N | N | N | N | N | Y            |
| CLOB                   | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y             | Y | Y | Y | N | N | N | N            |
| NCLOB                  | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y             | Y | Y | Y | N | N | N | N            |
| OBJECT                 | OBJECT                            | N             | N | N | N | N | N | N | N            |
| REF                    | REF                               | N             | N | N | N | N | N | N | N            |
| ARRAY                  | ARRAY                             | N             | N | N | N | N | N | N | N            |
| ROWID                  | ROWID                             | N             | N | N | N | N | N | N | N            |
| UROWID                 | UROWID                            | Y             | N | N | N | N | N | N | Y            |
| INTERVAL DAY TO SECOND | INTERVAL DAY TO SECOND            | N             | N | N | N | N | N | N | N            |
| INTERVAL YEAR TO MONTH | INTERVAL YEAR TO MONTH            | N             | N | N | N | N | N | N | N            |
| BIT                    | BIT                               | N             | N | N | Y | N | N | N | N            |
| JSON                   | JSON                              | N             | N | N | N | N | N | N | N            |

(7)

| **YashanDB Data Types** | **mysql mode Data Types**         | set<br/>Clob | set<br/>NClob | set<br/>Ascii<br/>Stream | set<br/>Character<br/>Stream | set<br/>Unicode<br/>Stream |
|------------------------|-----------------------------------| --- | --- | --- | --- | --- |
| RAW                    | BINARY/VARBINARY                  | N | N | N | Y | N |
| DATE                   | DATE                              | N | N | N | Y | N |
| TIMESTAMP              | TIMESTAMP                         | N | N | N | Y | N |
| TIME                   | TIME                              | N | N | N | Y | N |
| BLOB                   | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | N | N | N | Y | N |
| CLOB                   | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y   | N   | Y   | Y   | N   |
| NCLOB                  | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | Y | N   | Y | Y | N |
| OBJECT                 | OBJECT                            | N | N | N | N | N |
| REF                    | REF                               | N | N | N | N | N |
| ARRAY                  | ARRAY                             | N | N | N | N | N |
| ROWID                  | ROWID                             | N | N | N | Y | N |
| UROWID                 | UROWID                            | N | N | N | Y | N |
| INTERVAL DAY TO SECOND | INTERVAL DAY TO SECOND            | N | N | N | Y | N |
| INTERVAL YEAR TO MONTH | INTERVAL YEAR TO MONTH            | N | N | N | Y | N |
| BIT                    | BIT                               | N | N | N | Y | N |
| JSON                   | JSON                              | Y | N | Y | Y | N |

(8)

| **YashanDB Data Types** | **mysql mode Data Types** | set<br/>Binary<br/>Stream | set<br/>RowId | set<br/>NCharacter<br/>Stream | set<br/>NString | set<br/>Array | set<br/>SQL<br/>XML | set<br/>Ref | set<br/>URL |
|------------------------|------------------------|---------------------------|---------------| --- |-----------------| --- |---| --- | --- |
| RAW                    | BINARY/VARBINARY                    | Y                         | N             | N | Y               | N | N | N | N |
| DATE                   | DATE                   | N                         | N             | N | Y               | N | N | N | N |
| TIMESTAMP              | TIMESTAMP              | N                         | N             | N | Y               | N | N | N | N |
| TIME                   | TIME                   | N                         | N             | N | Y               | N | N | N | N |
| BLOB                   | BLOB                   | Y                         | N             | N | Y               | N | N | N | N |
| CLOB                   | CLOB                   | N                         | N             | N | Y               | N | N | N | N |
| NCLOB                  | NCLOB                  | N                         | N             | N   | Y               | N | N | N | N |
| OBJECT                 | OBJECT                 | N                         | N             | N | N               | N | N | N | N |
| REF                    | REF                    | N                         | N             | N | N               | N | N | N   | N |
| ARRAY                  | ARRAY                  | N                         | N             | N | N               | N   | N | N | N |
| ROWID                  | ROWID                  | N                         | Y             | N | Y               | N | N | N | N |
| UROWID                 | UROWID                 | Y                         | N             | N | Y               | N | N | N | N |
| INTERVAL DAY TO SECOND | INTERVAL DAY TO SECOND | N                         | N             | N | Y               | N | N | N | N |
| INTERVAL YEAR TO MONTH | INTERVAL YEAR TO MONTH | N                         | N             | N | Y               | N | N | N | N |
| BIT                    | BIT                    | N                         | N             | N | Y               | N | N | N | N |
| JSON                   | JSON                   | Y                         | N             | N | Y               | N | N | N | N |
