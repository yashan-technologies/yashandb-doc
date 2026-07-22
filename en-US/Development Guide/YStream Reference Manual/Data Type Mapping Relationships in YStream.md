The YStream client performs type conversion between YashanDB data types and Java data types.

## yashan Mode

> **Note**:
>
> In YashanDB deployed in yashan mode, when the USE_NATIVE_TYPE parameter is set to FALSE, Oracle - compatible data types will be adopted. Refer to the following table for the compatible types, such as NUMBER and FLOAT.

When the YStream client connects to YashanDB deployed in yashan mode, the **adapted** data types are shown in the following table.

|YashanDB Data Type |YasTypes |JDBC Type |Standard Java Data Type |
|------------------------|-----------------------|----------------------------------------------|--------------------------------------|
| CHAR                   | YasTypes.CHAR         | java.sql.Types.CHAR                          | java.lang.String                     |
| VARCHAR                | YasTypes.VARCHAR      | java.sql.Types.VARCHAR                       | java.lang.String                     |
| NCHAR                  | YasTypes.NCHAR        | java.sql.Types.NCHAR                         | java.lang.String                     |
| NVARCHAR               | YasTypes.NVARCHAR     | java.sql.Types.NVARCHAR                      | java.lang.String                     |
| BOOLEAN                | YasTypes.BOOLEAN      | java.sql.Types.BOOLEAN                       | java.lang.Boolean                    |
| TINYINT/TINYINT UNSIGNED | YasTypes.TINYINT      | java.sql.Types.TINYINT                       | java.lang.Integer                    |
| SMALLINT/SMALLINT UNSIGNED | YasTypes.SMALLINT     | java.sql.Types.SMALLINT                      | java.lang.Integer                    |
| INTEGER/INTEGER UNSIGNED | YasTypes.INTEGER      | java.sql.Types.INTEGER                       | java.lang.Integer                    |
| BIGINT/BIGINT UNSIGNED | YasTypes.BIGINT       | java.sql.Types.BIGINT                        | java.lang.Long                       |
| FLOAT                  | YasTypes.REAL         | java.sql.Types.REAL                          | java.lang.Float                      |
| DOUBLE                 | YasTypes.DOUBLE       | java.sql.Types.DOUBLE                        | java.lang.Double                     |
| DOUBLE                 | YasTypes.FLOAT        | java.sql.Types.FLOAT                         | java.lang.Double                     |
| NUMBER                 | YasTypes.NUMBER       | java.sql.Types.NUMERIC                       | java.math.BigDecimal                 |
| NUMBER                 | YasTypes.DECIMAL      | java.sql.Types.DECIMAL                       | java.math.BigDecimal                 |
| RAW                    | YasTypes.BINARY       | java.sql.Types.BINARY                        | byte\[\]                             |
| RAW                    | YasTypes.VARBINARY    | java.sql.Types.VARBINARY                     | byte\[\]                             |
| DATE                   | YasTypes.DATE         | java.sql.Types.DATE                          | java.sql.Date                        |
| TIME                   | YasTypes.TIME         | java.sql.Types.TIME                          | java.sql.Time                        |
| TIMESTAMP              | YasTypes.TIMESTAMP    | java.sql.Types.TIMESTAMP                     | java.sql.Timestamp                   |
| BLOB                   | YasTypes.BLOB         | java.sql.Types.BLOB                          | byte\[\]                             |
| CLOB                   | YasTypes.CLOB         | java.sql.Types.CLOB                          | java.lang.String                     |
| INTERVAL YEAR TO MONTH | YasTypes.YM\_INTERVAL | java.sql.Types.OTHER                         | \--                                  |
| INTERVAL DAY TO SECOND | YasTypes.DS\_INTERVAL | java.sql.Types.OTHER                         | \--                                  |
| TIMESTAMP WITH TIME ZONE                | YasTypes.TIMESTAMP_TZ  | java.sql.Types.TIMESTAMP\_WITH\_TIMEZONE | java.time.OffsetDateTime                     |
| TIMESTAMP WITH LOCAL TIME ZONE          | YasTypes.TIMESTAMP_LTZ | java.sql.Types.TIMESTAMP                 | javal.sql.Timestamp                      |
| BIT                    | YasTypes.BIT          | java.sql.Types.BIT                           | BIT(1):boolean<br>BIT(2~64):byte\[\] |


## mysql Mode

When the YStream client connects to YashanDB deployed in mysql mode, the **adapted** data types are shown in the following table.

|Data Type in mysql mode |mysqlType |JDBC Type |Standard Java Data Type |
|------------------------|-----------------------|----------------------------------------------|--------------------------------------|
| TINYINT                | 2      | java.sql.Types.TINYINT                       | java.lang.Integer                    |
| SMALLINT               | 3     | java.sql.Types.SMALLINT                      | java.lang.Integer                    |
| INTEGER                | 5      | java.sql.Types.INTEGER                       | java.lang.Integer                    |
| BIGINT                 | 6       | java.sql.Types.BIGINT                        | java.lang.Long                       |
| FLOAT                  | 7         | java.sql.Types.REAL                          | java.lang.Float                      |
| DOUBLE                 | 8       | java.sql.Types.DOUBLE                        | java.lang.Double                     |
| DECIMAL          | 9      | java.sql.Types.DECIMAL                       | java.math.BigDecimal                 |
| CHAR | 10 | java.sql.Types.CHAR | java.lang.String |
| VARCHAR | 11 | java.sql.Types.VARCHAR | java.lang.String |
| TINYTEXT                                | 12                      | java.sql.Types.CLOB      | java.lang.String                             |
| MEDIUMTEXT                              | 13                      | java.sql.Types.CLOB      | java.lang.String                             |
| TEXT                                    | 14                      | java.sql.Types.CLOB      | java.lang.String                             |
| LONGTEXT                                | 15                      | java.sql.Types.CLOB      | java.lang.String                             |
| BINARY                                  | 16                      | java.sql.Types.BINARY    | byte\[\]                                     |
| VARBINARY                               | 17                      | java.sql.Types.VARBINARY | byte\[\]                                     |
| TINYBLOB                                | 18                      | java.sql.Types.BLOB      | byte\[\]                                     |
| MEDIUMBLOB                              | 19                      | java.sql.Types.BLOB      | byte\[\]                                     |
| BLOB                                    | 20                      | java.sql.Types.BLOB      | byte\[\]                                     |
| LONGBLOB                                | 21                      | java.sql.Types.BLOB      | byte\[\]                                     |
| DATE                   | 22        | java.sql.Types.DATE                          | java.sql.Date                        |
| TIME                   | 23        | java.sql.Types.TIME                          | java.sql.Time                        |
| DATETIME | 24 | java.sql.Types.TIMESTAMP | java.sql.Timestamp |
| TIMESTAMP     | 25   | java.sql.Types.TIMESTAMP                     | java.sql.Timestamp                   |
| TINYINT UNSIGNED | 65 | java.sql.Types.TINYINT | java.lang.Integer |
| SMALLINT UNSIGNED | 66 | java.sql.Types.SMALLINT | java.lang.Integer |
| INTEGER UNSIGNED | 68 | java.sql.Types.INTEGER | java.lang.Integer |
| BIGINT UNSIGNED | 69 | java.sql.Types.BIGINT | java.lang.Long |
| FLOAT UNSIGNED | 70 | java.sql.Types.REAL | java.lang.Float |
| DOUBLE UNSIGNED | 71 | java.sql.Types.DOUBLE | java.lang.Double |
| DECIMAL UNSIGNED | 72 | java.sql.Types.DECIMAL | java.math.BigDecimal |
