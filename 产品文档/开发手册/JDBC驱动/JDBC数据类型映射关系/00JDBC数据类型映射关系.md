YashanDB  JDBC驱动会对YashanDB数据类型和Java数据类型进行类型转换，转换关系如下表所示：

| YashanDB数据类型           | YasTypes               | JDBC类型 | 标准Java数据类型                           | 是否已支持 |
|------------------------|------------------------| --- |--------------------------------------|-------|
| CHAR                   | YasTypes.CHAR          | java.sql.Types.CHAR | java.lang.String                     | Y     |
| VARCHAR                | YasTypes.VARCHAR       | java.sql.Types.VARCHAR | java.lang.String                     | Y     |
| NCHAR                  | YasTypes.NCHAR         | java.sql.Types.NCHAR | java.lang.String                     | N     |
| NVARCHAR               | YasTypes.NVARCHAR      | java.sql.Types.NVARCHAR | java.lang.String                     | N     |
| BOOLEAN                | YasTypes.BOOLEAN       | java.sql.Types.BOOLEAN | java.lang.Boolean                    | Y     |
| TINYINT                | YasTypes.TINYINT       | java.sql.Types.TINYINT | java.lang.Integer                    | Y     |
| SMALLINT               | YasTypes.SMALLINT      | java.sql.Types.SMALLINT | java.lang.Integer                    | Y     |
| INTEGER                | YasTypes.INTEGER       | java.sql.Types.INTEGER | java.lang.Integer                    | Y     |
| BIGINT                 | YasTypes.BIGINT        | java.sql.Types.BIGINT | java.lang.Long                       | Y     |
| FLOAT                  | YasTypes.REAL          | java.sql.Types.REAL | java.lang.Float                      | Y     |
| DOUBLE                 | YasTypes.DOUBLE        | java.sql.Types.DOUBLE | java.lang.Double                     | Y     |
| DOUBLE                 | YasTypes.FLOAT         | java.sql.Types.FLOAT | java.lang.Double                     | Y     |
| NUMBER                 | YasTypes.NUMBER        | java.sql.Types.NUMERIC | java.math.<br>BigDecimal             | Y     |
| NUMBER                 | YasTypes.DECIMAL       | java.sql.Types.DECIMAL | java.math.<br>BigDecimal             | Y     |
| RAW                    | YasTypes.BINARY        | java.sql.Types.BINARY | byte\[\]                             | N     |
| RAW                    | YasTypes.VARBINARY     | java.sql.Types.VARBINARY | byte\[\]                             | N     |
| DATE                   | YasTypes.DATE          | java.sql.Types.DATE | java.sql.Date                        | Y     |
| TIME                   | YasTypes.TIME          | java.sql.Types.TIME | java.sql.Time                        | Y     |
| TIMESTAMP              | YasTypes.TIMESTAMP     | java.sql.Types.TIMESTAMP | javal.sql.<br>Timestamp              | Y     |
| BLOB                   | YasTypes.BLOB          | java.sql.Types.BLOB | java.sql.Stream                      | N     |
| BLOB                   | YasTypes.BLOB          | java.sql.Types.BLOB | java.sql.Blob                        | Y     |
| CLOB                   | YasTypes.CLOB          | java.sql.Types.CLOB | java.sql.Clob                        | Y     |
| STRUCT                 | YasTypes.STRUCT        | java.sql.Types.STRUCT | java.sql.Struct                      | N     |
| REF                    | YasTypes.REF           | java.sql.Types.REF | java.sql.Ref                         | N     |
| ARRAY                  | YasTypes.ARRAY         | java.sql.Types.ARRAY | java.sql.Array                       | N     |
| ROWID                  | YasTypes.ROWID         | java.sql.Types.ROWID | java.sql.RowId                       | Y     |
| INTERVAL YEAR TO MONTH | YasTypes.              | java.sql.Types.OTHER | \--                                  | N     |
| INTERVAL DAY TO SECOND | YasTypes.              | java.sql.Types.OTHER | \--                                  | N     |
| TIMESTAMP WITH TIME ZONE | YasTypes.TIMESTAMP_TZ  | java.sql.Types.<br>TIMESTAMP_WITH_TIMEZONE | java.time.OffsetDateTime | Y     |
| TIMESTAMP WITH LOCAL TIME ZONE | YasTypes.TIMESTAMP_LTZ | java.sql.Types.TIMESTAMP | javal.sql.<br>Timestamp | Y     |
| BIT                    | YasTypes.BIT           | java.sql.Types.BIT | BIT(1):boolean<br>BIT(2~64):byte\[\] | Y     |
| CURSOR                 | YasTypes.CURSOR        | java.sql.Types.REF_CURSROR | java.sql.ResultSet                   | Y     |
| CURSOR                 | YasTypes.REF_CURSOR    | java.sql.Types.REF_CURSROR | java.sql.ResultSet                   | Y     |
| JSON                   | YasTypes.JSON          | java.sql.Types.OTHER | \--                                  | Y     |
| XMLTYPE                | YasTypes.SQLXML        | java.sql.Types.SQLXML | java.sql.SQLXML                      | Y     |
