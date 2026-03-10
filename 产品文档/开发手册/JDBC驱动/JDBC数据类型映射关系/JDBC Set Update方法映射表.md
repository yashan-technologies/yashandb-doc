(1)

| YashanDB/<br>JDBC | set<br/>String | set<br/>Object | set<br/>Null | set<br/>Boolean | set<br/>Int | set<br/>Byte | set<br/>Short | set<br/>Long |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| VARCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| NCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| NVARCHAR | Y   | Y   | Y   | Y | Y | Y | Y | Y |
| BOOLEAN | Y   | Y   | Y   | Y   | Y | Y | Y | Y |
| TINYINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| SMALLINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| INTEGER | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| BIGINT | Y   | Y   | Y   | Y | Y   | Y   | Y   | Y   |
| FLOAT | Y   | Y   | Y   | N | Y   | Y   | Y   | Y   |
| DOUBLE | Y   | Y   | Y   | N | Y   | Y   | Y   | Y   |
| NUMBER | Y | Y   | Y   | Y | Y   | Y   | Y   | Y   |

(2)

| YashanDB/<br/>JDBC | set<br/>Bytes | set<br/>Float | set<br/>Double | set<br/>Big<br>Decimal | set<br/>Date | set<br/>Time | set<br/>Timestamp | set<br/>Blob |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| VARCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| NCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| NVARCHAR | Y | Y | Y | Y | Y | Y | Y | Y |
| BOOLEAN | Y | N | N | Y | N | N | N | N |
| TINYINT | N   | Y   | Y   | Y | N | N | N | N |
| SMALLINT | N   | Y   | Y   | Y | N | N | N | N |
| INTEGER | N   | Y   | Y   | Y | N | N | N | N |
| BIGINT | N   | Y   | Y   | Y | N | N | N | N |
| FLOAT | N   | Y   | Y   | Y | N | N | N | N |
| DOUBLE | N   | Y   | Y   | Y | N | N | N | N |
| NUMBER | N   | Y   | Y   | Y   | N | N | N | N |

(3)

| YashanDB/<br/>JDBC | set<br/>Clob | set<br/>NClob | set<br/>Ascii<br>Stream | set<br/>Character<br>Stream | set<br/>Unicode<br>Stream |
| --- | --- | --- | --- | --- | --- |
| CHAR | Y | N | Y | Y | N |
| VARCHAR | Y | N | Y | Y | N |
| NCHAR | Y | N | Y | Y | N |
| NVARCHAR | Y | N | Y | Y | N |
| BOOLEAN | N | N | N | Y | N |
| TINYINT | N | N | N | Y | N |
| SMALLINT | N | N | N | Y | N |
| INTEGER | N | N | N | Y | N |
| BIGINT | N | N | N | Y | N |
| FLOAT | N | N | N | Y | N |
| DOUBLE | N | N | N | Y | N |
| NUMBER | N | N | N | Y | N |

(4)

| YashanDB/<br/>**JDBC** | set<br/>Binary<br>Stream | set<br/>RowId | set<br/>NCharacter<br>Stream | set<br/>NString | set<br/>Array | set<br/>SQL<br/>XML | set<br/>Ref | set<br/>URL |
| --- | --- | --- | --- | --- | --- |---| --- | --- |
| CHAR | Y | Y | N | Y | N | Y | N | N |
| VARCHAR | Y | Y | N | Y | N | Y | N | N |
| NCHAR | Y | Y | N | Y | N | Y | N | N |
| NVARCHAR | Y | Y | N | Y   | N | Y | N | N |
| BOOLEAN | N | N | N | Y | N | N | N | N |
| TINYINT | N | N | N | Y | N | N | N | N |
| SMALLINT | N | N | N | Y | N | N | N | N |
| INTEGER | N | N | N | Y | N | N | N | N |
| BIGINT | N | N | N | Y | N | N | N | N |
| FLOAT | N | N | N | Y | N | N | N | N |
| DOUBLE | N | N | N | Y | N | N | N | N |
| NUMBER | N | N | N | Y | N | N | N | N |

(5)

| YashanDB/<br/>JDBC     | set<br/>String | set<br/>Object | set<br/>Null | set<br/>Boolean | set<br/>Int | set<br/>Byte | set<br/>Short | set<br/>Long |
|------------------------| --- | --- | --- | --- | --- | --- | --- | --- |
| RAW                    | Y | Y   | Y   | N | N | N | N | N |
| DATE                   | Y | Y   | Y   | N | N | N | N | N |
| TIMESTAMP              | Y | Y   | Y   | N | N | N | N | N |
| TIME                   | Y | Y   | Y   | N | N | N | N | N |
| BLOB                   | Y | Y   | Y   | N | N | N | N | N |
| CLOB                   | Y | Y   | Y   | N | Y | Y | Y | Y |
| NCLOB                  | Y | Y   | Y   | N | Y | Y | Y | Y |
| OBJECT                 | N | N   | N   | N | N | N | N | N |
| REF                    | N | N   | N   | N | N | N | N | N |
| ARRAY                  | N | N   | N   | N | N | N | N | N |
| ROWID                  | Y | Y   | Y   | N | N | N | N | N |
| UROWID                 | Y | Y   | Y   | N | N | N | N | N |
| INTERVAL DAY TO SECOND | Y | Y   | Y   | N | N | N | N | N |
| INTERVAL YEAR TO MONTH | Y | Y   | Y   | N | N | N | N | N |
| BIT                    | Y | Y | Y | Y | Y | Y | Y | Y |
| JSON                   | Y | Y | Y | N | N | N | N | N |
| XMLTYPE                | Y | Y | Y | N | N | N | N | N |

(6)

| YashanDB/<br/>JDBC     | set<br/>Bytes | set<br/>Float | set<br/>Double | setBig<br/>Decimal | set<br/>Date | set<br/>Time | set<br/>Timestamp | set<br/>Blob |
|------------------------|---------------| --- | --- | --- | --- | --- | --- |--------------|
| RAW                    | Y             | N | N | N | N | N | N | Y            |
| DATE                   | N             | N | N | N | Y   | Y   | Y | N            |
| TIMESTAMP              | N             | N | N | N | Y   | Y | Y   | N            |
| TIME                   | N             | N | N | N | Y   | Y | Y   | N            |
| BLOB                   | Y             | N | N | N | N | N | N | Y            |
| CLOB                   | Y             | Y | Y | Y | N | N | N | N            |
| NCLOB                  | Y             | Y | Y | Y | N | N | N | N            |
| OBJECT                 | N             | N | N | N | N | N | N | N            |
| REF                    | N             | N | N | N | N | N | N | N            |
| ARRAY                  | N             | N | N | N | N | N | N | N            |
| ROWID                  | N             | N | N | N | N | N | N | N            |
| UROWID                 | Y             | N | N | N | N | N | N | Y            |
| INTERVAL DAY TO SECOND | N             | N | N | N | N | N | N | N            |
| INTERVAL YEAR TO MONTH | N             | N | N | N | N | N | N | N            |
| BIT                    | N             | N | N | Y | N | N | N | N            |
| JSON                   | N             | N | N | N | N | N | N | N            |
| XMLTYPE                | N             | N | N | N | N | N | N | N            |

(7)

| YashanDB/<br/>JDBC     | set<br/>Clob | set<br/>NClob | set<br/>Ascii<br/>Stream | set<br/>Character<br/>Stream | set<br/>Unicode<br/>Stream |
|------------------------| --- | --- | --- | --- | --- |
| RAW                    | N | N | N | Y | N |
| DATE                   | N | N | N | Y | N |
| TIMESTAMP              | N | N | N | Y | N |
| TIME                   | N | N | N | Y | N |
| BLOB                   | N | N | N | Y | N |
| CLOB                   | Y   | N   | Y   | Y   | N   |
| NCLOB                  | Y | N   | Y | Y | N |
| OBJECT                 | N | N | N | N | N |
| REF                    | N | N | N | N | N |
| ARRAY                  | N | N | N | N | N |
| ROWID                  | N | N | N | Y | N |
| UROWID                 | N | N | N | Y | N |
| INTERVAL DAY TO SECOND | N | N | N | Y | N |
| INTERVAL YEAR TO MONTH | N | N | N | Y | N |
| BIT                    | N | N | N | Y | N |
| JSON                   | Y | N | Y | Y | N |
| XMLTYPE                | Y | N | Y | Y | N |

(8)

| YashanDB/<br/>JDBC     | set<br/>Binary<br/>Stream | set<br/>RowId | set<br/>NCharacter<br/>Stream | set<br/>NString | set<br/>Array | set<br/>SQL<br/>XML | set<br/>Ref | set<br/>URL |
|------------------------|---------------------------|---------------| --- |-----------------| --- |---| --- | --- |
| RAW                    | Y                         | N             | N | Y               | N | N | N | N |
| DATE                   | N                         | N             | N | Y               | N | N | N | N |
| TIMESTAMP              | N                         | N             | N | Y               | N | N | N | N |
| TIME                   | N                         | N             | N | Y               | N | N | N | N |
| BLOB                   | Y                         | N             | N | Y               | N | N | N | N |
| CLOB                   | N                         | N             | N | Y               | N | N | N | N |
| NCLOB                  | N                         | N             | N   | Y               | N | N | N | N |
| OBJECT                 | N                         | N             | N | N               | N | N | N | N |
| REF                    | N                         | N             | N | N               | N | N | N   | N |
| ARRAY                  | N                         | N             | N | N               | N   | N | N | N |
| ROWID                  | N                         | Y             | N | Y               | N | N | N | N |
| UROWID                 | Y                         | N             | N | Y               | N | N | N | N |
| INTERVAL DAY TO SECOND | N                         | N             | N | Y               | N | N | N | N |
| INTERVAL YEAR TO MONTH | N                         | N             | N | Y               | N | N | N | N |
| BIT                    | N                         | N             | N | Y               | N | N | N | N |
| JSON                   | Y                         | N             | N | Y               | N | N | N | N |
| XMLTYPE                | N                         | N             | N | N               | N | Y | N | N |