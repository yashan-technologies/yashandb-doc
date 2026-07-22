YashanDB JDBC driver will convert YashanDB data types and Java data types.

## yashan Mode

> **Note**:
>
> In YashanDB deployed in yashan mode, when the USE_NATIVE_TYPE parameter is set to FALSE, Oracle - compatible data types will be adopted. Refer to the following table for the correspondence based on the compatible types, such as NUMBER and FLOAT.

When the YashanDB JDBC driver connects to YashanDB running in yashan mode, the **adapted** data types are shown in the following table.

|Data Type in yashan mode |YasTypes |JDBC Type |Standard Java Data Type |
|--------------------------------|------------------------|----------------------------------------------|--------------------------------------|
| CHAR                           | YasTypes.CHAR          | java.sql.Types.CHAR                          | java.lang.String                     |
| VARCHAR                        | YasTypes.VARCHAR       | java.sql.Types.VARCHAR                       | java.lang.String                     |
| NCHAR                          | YasTypes.NCHAR         | java.sql.Types.NCHAR                         | java.lang.String                     |
| NVARCHAR                       | YasTypes.NVARCHAR      | java.sql.Types.NVARCHAR                      | java.lang.String                     |
| BOOLEAN                        | YasTypes.BOOLEAN       | java.sql.Types.BOOLEAN                       | java.lang.Boolean                    |
| TINYINT                        | YasTypes.TINYINT       | java.sql.Types.TINYINT                       | java.lang.Integer                    |
| SMALLINT                       | YasTypes.SMALLINT      | java.sql.Types.SMALLINT                      | java.lang.Integer                    |
| INTEGER                        | YasTypes.INTEGER       | java.sql.Types.INTEGER                       | java.lang.Integer                    |
| BIGINT                         | YasTypes.BIGINT        | java.sql.Types.BIGINT                        | java.lang.Long                       |
| FLOAT                          | YasTypes.REAL          | java.sql.Types.REAL                          | java.lang.Float                      |
| DOUBLE                         | YasTypes.DOUBLE        | java.sql.Types.DOUBLE                        | java.lang.Double                     |
| DOUBLE                         | YasTypes.FLOAT         | java.sql.Types.FLOAT                         | java.lang.Double                     |
| NUMBER                         | YasTypes.NUMBER        | java.sql.Types.NUMERIC                       | java.math.<br>BigDecimal             |
| NUMBER                         | YasTypes.DECIMAL       | java.sql.Types.DECIMAL                       | java.math.<br>BigDecimal             |
| BIT | YasTypes.BIT | java.sql.Types.BIT | BIT(1):boolean<br>BIT(2~64):byte\[\] |
| RAW                            | YasTypes.BINARY        | java.sql.Types.BINARY                        | byte\[\]                             |
| RAW                            | YasTypes.VARBINARY     | java.sql.Types.VARBINARY                     | byte\[\]                             |
| DATE                           | YasTypes.DATE          | java.sql.Types.DATE                          | java.sql.Date                        |
| TIME                           | YasTypes.TIME          | java.sql.Types.TIME                          | java.sql.Time                        |
| INTERVAL YEAR TO MONTH | YasTypes.YM_INTERVAL | java.sql.Types.OTHER | \-- |
| INTERVAL DAY TO SECOND | YasTypes.DS_INTERVAL | java.sql.Types.OTHER | \-- |
| TIMESTAMP | YasTypes.TIMESTAMP | java.sql.Types.TIMESTAMP | javal.sql.<br>Timestamp |
| TIMESTAMP WITH TIME ZONE | YasTypes.TIMESTAMP_TZ | java.sql.Types.TIMESTAMP\_WITH\_TIMEZONE | java.time.OffsetDateTime |
| TIMESTAMP WITH LOCAL TIME ZONE | YasTypes.TIMESTAMP_LTZ | java.sql.Types.TIMESTAMP | javal.sql.<br>Timestamp |
| BLOB                           | YasTypes.BLOB          | java.sql.Types.BLOB                          | java.sql.Blob                        |
| CLOB                           | YasTypes.CLOB          | java.sql.Types.CLOB                          | java.sql.Clob                        |
| NCLOB                          | YasTypes.NCLOB         | java.sql.Types.NCLOB                         | java.sql.NClob                       |
| XMLTYPE | YasTypes.XMLTYPE | java.sql.Types.SQLXML | java.sql.SQLXML |
| STRUCT                         | YasTypes.STRUCT        | java.sql.Types.STRUCT                        | java.sql.Struct                      |
| ARRAY                          | YasTypes.ARRAY         | java.sql.Types.ARRAY                         | java.sql.Array                       |
| ROWID                          | YasTypes.ROWID         | java.sql.Types.ROWID                         | java.sql.RowId                       |
| CURSOR                         | YasTypes.CURSOR        | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   |
| CURSOR                         | YasTypes.REF_CURSOR    | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   |
| JSON                           | YasTypes.JSON          | java.sql.Types.OTHER                         | \--                                  |



## mysql Mode

When the YashanDB JDBC driver connects to YashanDB running in mysql mode, the **adapted** data types are as shown in the following table.

|Data Type in mysql mode |YasTypes |JDBC Type |Standard Java Data Type |
|-----------------------------------|------------------------|----------------------------------------------|--------------------------------------|
| CHAR                              | YasTypes.CHAR          | java.sql.Types.CHAR                          | java.lang.String                     |
| VARCHAR                           | YasTypes.VARCHAR       | java.sql.Types.VARCHAR                       | java.lang.String                     |
| BOOLEAN                           | YasTypes.BOOLEAN       | java.sql.Types.BOOLEAN                       | java.lang.Boolean                    |
| TINYINT/TINYINT UNSIGNED          | YasTypes.TINYINT       | java.sql.Types.TINYINT                       | java.lang.Integer                    |
| SMALLINT/SMALLINT UNSIGNED        | YasTypes.SMALLINT      | java.sql.Types.SMALLINT                      | java.lang.Integer                    |
| INTEGER/INTEGER UNSIGNED          | YasTypes.INTEGER       | java.sql.Types.INTEGER                       | java.lang.Integer                    |
| BIGINT/BIGINT UNSIGNED            | YasTypes.BIGINT        | java.sql.Types.BIGINT                        | java.lang.Long                       |
| FLOAT                             | YasTypes.REAL          | java.sql.Types.REAL                          | java.lang.Float                      |
| DOUBLE                            | YasTypes.DOUBLE        | java.sql.Types.DOUBLE                        | java.lang.Double                     |
| DECIMAL                     | YasTypes.DECIMAL       | java.sql.Types.DECIMAL                       | java.math.<br>BigDecimal             |
| BINARY                            | YasTypes.BINARY        | java.sql.Types.BINARY                        | byte\[\]                             |
| VARBINARY                         | YasTypes.VARBINARY     | java.sql.Types.VARBINARY                     | byte\[\]                             |
| DATE                              | YasTypes.DATE          | java.sql.Types.DATE                          | java.sql.Date                        |
| TIME                              | YasTypes.TIME          | java.sql.Types.TIME                          | java.sql.Time                        |
| TIMESTAMP/DATETIME                | YasTypes.TIMESTAMP     | java.sql.Types.TIMESTAMP                     | javal.sql.<br>Timestamp              |
| TINYBLOB/BLOB/MEDIUMBLOB/LONGBLOB | YasTypes.BLOB          | java.sql.Types.BLOB                          | java.sql.Blob                        |
| TINYTEXT/TEXT/MEDIUMTEXT/LONGTEXT | YasTypes.CLOB          | java.sql.Types.CLOB                          | java.sql.Clob                        |
| STRUCT                            | YasTypes.STRUCT        | java.sql.Types.STRUCT                        | java.sql.Struct                      |
| ARRAY                             | YasTypes.ARRAY         | java.sql.Types.ARRAY                         | java.sql.Array                       |
| CURSOR                            | YasTypes.CURSOR        | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   |
| CURSOR                            | YasTypes.REF_CURSOR    | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   |
