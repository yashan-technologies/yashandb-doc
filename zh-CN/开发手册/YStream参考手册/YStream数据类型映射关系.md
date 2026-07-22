YStream客户端会对YashanDB数据类型和Java数据类型进行类型转换。

## yashan模式

> **Note**:
>
> 在部署为yashan模式的YashanDB中，当USE_NATIVE_TYPE参数设置为FALSE时，将采用Oracle兼容数据类型，依据兼容类型在下表进行对照，例如NUMBER和FLOAT。

YStream客户端对接部署为yashan模式的YashanDB时，**已适配**的数据类型如下表所示。

|  YashanDB数据类型| YasTypes| JDBC类型| 标准Java数据类型|
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


## mysql模式

YStream客户端对接部署为mysql模式的YashanDB时，**已适配**的数据类型如下表所示。

|  mysql模式数据类型| mysqlType| JDBC类型| 标准Java数据类型|
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
