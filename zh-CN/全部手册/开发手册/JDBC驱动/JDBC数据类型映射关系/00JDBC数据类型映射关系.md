YashanDB  JDBC驱动会对YashanDB数据类型和Java数据类型进行类型转换，转换关系如下表所示：

> **Note**:
>
> - mysql模式数据类型特指YashanDB运行于mysql模式时的SQL数据类型。
> - 当USE_NATIVE_TYPE参数设置为FALSE时，YashanDB将采用Oracle兼容数据类型（mysql模式下忽略此设定，不会采用Oracle兼容数据类型），依据兼容类型在下表进行对照，例如NUMBER和FLOAT。

|  YashanDB数据类型| mysql模式数据类型| YasTypes| JDBC类型| 标准Java数据类型| 是否已支持|
|--------------------------------|-----------------------------------|------------------------|----------------------------------------------|--------------------------------------|-------|
| CHAR                           | CHAR                              | YasTypes.CHAR          | java.sql.Types.CHAR                          | java.lang.String                     | Y     |
| VARCHAR                        | VARCHAR                           | YasTypes.VARCHAR       | java.sql.Types.VARCHAR                       | java.lang.String                     | Y     |
| NCHAR                          | NCHAR                             | YasTypes.NCHAR         | java.sql.Types.NCHAR                         | java.lang.String                     | N     |
| NVARCHAR                       | NVARCHAR                          | YasTypes.NVARCHAR      | java.sql.Types.NVARCHAR                      | java.lang.String                     | N     |
| BOOLEAN                        | BOOLEAN                           | YasTypes.BOOLEAN       | java.sql.Types.BOOLEAN                       | java.lang.Boolean                    | Y     |
| TINYINT                        | TINYINT                           | YasTypes.TINYINT       | java.sql.Types.TINYINT                       | java.lang.Integer                    | Y     |
| SMALLINT                       | SMALLINT                          | YasTypes.SMALLINT      | java.sql.Types.SMALLINT                      | java.lang.Integer                    | Y     |
| INTEGER                        | INTEGER                           | YasTypes.INTEGER       | java.sql.Types.INTEGER                       | java.lang.Integer                    | Y     |
| BIGINT                         | BIGINT                            | YasTypes.BIGINT        | java.sql.Types.BIGINT                        | java.lang.Long                       | Y     |
| FLOAT                          | FLOAT                             | YasTypes.REAL          | java.sql.Types.REAL                          | java.lang.Float                      | Y     |
| DOUBLE                         | DOUBLE                            | YasTypes.DOUBLE        | java.sql.Types.DOUBLE                        | java.lang.Double                     | Y     |
| DOUBLE                         | DOUBLE                            | YasTypes.FLOAT         | java.sql.Types.FLOAT                         | java.lang.Double                     | Y     |
| NUMBER                         | NUMBER                            | YasTypes.NUMBER        | java.sql.Types.NUMERIC                       | java.math.<br>BigDecimal             | Y     |
| NUMBER                         | NUMBER                            | YasTypes.DECIMAL       | java.sql.Types.DECIMAL                       | java.math.<br>BigDecimal             | Y     |
| RAW                            | BINARY                            | YasTypes.BINARY        | java.sql.Types.BINARY                        | byte\[\]                             | N     |
| RAW                            | VARBINARY                         | YasTypes.VARBINARY     | java.sql.Types.VARBINARY                     | byte\[\]                             | N     |
| DATE                           | DATE                              | YasTypes.DATE          | java.sql.Types.DATE                          | java.sql.Date                        | Y     |
| TIME                           | TIME                              | YasTypes.TIME          | java.sql.Types.TIME                          | java.sql.Time                        | Y     |
| TIMESTAMP                      | TIMESTAMP                         | YasTypes.TIMESTAMP     | java.sql.Types.TIMESTAMP                     | javal.sql.<br>Timestamp              | Y     |
| BLOB                           | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | YasTypes.BLOB          | java.sql.Types.BLOB                          | java.sql.Stream                      | N     |
| BLOB                           | BLOB/LONGBLOB/MEDIUMBLOB/TINYBLOB | YasTypes.BLOB          | java.sql.Types.BLOB                          | java.sql.Blob                        | Y     |
| CLOB                           | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | YasTypes.CLOB          | java.sql.Types.CLOB                          | java.sql.Clob                        | Y     |
| NCLOB                          | TEXT/LONGTEXT/MEDIUMTEXT/TINYTEXT | YasTypes.NCLOB         | java.sql.Types.NCLOB                         | java.sql.NClob                       | Y     |
| STRUCT                         | STRUCT                            | YasTypes.STRUCT        | java.sql.Types.STRUCT                        | java.sql.Struct                      | N     |
| REF                            | REF                               | YasTypes.REF           | java.sql.Types.REF                           | java.sql.Ref                         | N     |
| ARRAY                          | ARRAY                             | YasTypes.ARRAY         | java.sql.Types.ARRAY                         | java.sql.Array                       | N     |
| ROWID                          | ROWID                             | YasTypes.ROWID         | java.sql.Types.ROWID                         | java.sql.RowId                       | Y     |
| INTERVAL YEAR TO MONTH         | INTERVAL YEAR TO MONTH            | YasTypes.              | java.sql.Types.OTHER                         | \--                                  | N     |
| INTERVAL DAY TO SECOND         | INTERVAL DAY TO SECOND            | YasTypes.              | java.sql.Types.OTHER                         | \--                                  | N     |
| TIMESTAMP WITH TIME ZONE       | TIMESTAMP WITH TIME ZONE          | YasTypes.TIMESTAMP_TZ  | java.sql.Types.<br>TIMESTAMP_WITH_TIMEZONE | java.time.OffsetDateTime             | Y     |
| TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH LOCAL TIME ZONE    | YasTypes.TIMESTAMP_LTZ | java.sql.Types.TIMESTAMP                     | javal.sql.<br>Timestamp              | Y     |
| BIT                            | BIT                               | YasTypes.BIT           | java.sql.Types.BIT                           | BIT(1):boolean<br>BIT(2~64):byte\[\] | Y     |
| CURSOR                         | CURSOR                            | YasTypes.CURSOR        | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   | Y     |
| CURSOR                         | CURSOR                            | YasTypes.REF_CURSOR    | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   | Y     |
| JSON                           | JSON                              | YasTypes.JSON          | java.sql.Types.OTHER                         | \--                                  | Y     |
