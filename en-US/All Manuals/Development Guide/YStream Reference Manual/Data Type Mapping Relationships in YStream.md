The YStream client performs type conversion between YashanDB data types and Java data types, as shown in the table below:

|YashanDB Data Type |YasTypes |JDBC Type |Standard Java Data Type |Supported |
|------------------------|-----------------------|----------------------------------------------|--------------------------------------|-------|
| CHAR                   | YasTypes.CHAR         | java.sql.Types.CHAR                          | java.lang.String                     | Y     |
| VARCHAR                | YasTypes.VARCHAR      | java.sql.Types.VARCHAR                       | java.lang.String                     | Y     |
| NCHAR                  | YasTypes.NCHAR        | java.sql.Types.NCHAR                         | java.lang.String                     | Y     |
| NVARCHAR               | YasTypes.NVARCHAR     | java.sql.Types.NVARCHAR                      | java.lang.String                     | Y     |
| BOOLEAN                | YasTypes.BOOLEAN      | java.sql.Types.BOOLEAN                       | java.lang.Boolean                    | Y     |
| TINYINT                | YasTypes.TINYINT      | java.sql.Types.TINYINT                       | java.lang.Integer                    | Y     |
| SMALLINT               | YasTypes.SMALLINT     | java.sql.Types.SMALLINT                      | java.lang.Integer                    | Y     |
| INTEGER                | YasTypes.INTEGER      | java.sql.Types.INTEGER                       | java.lang.Integer                    | Y     |
| BIGINT                 | YasTypes.BIGINT       | java.sql.Types.BIGINT                        | java.lang.Long                       | Y     |
| FLOAT                  | YasTypes.REAL         | java.sql.Types.REAL                          | java.lang.Float                      | Y     |
| DOUBLE                 | YasTypes.DOUBLE       | java.sql.Types.DOUBLE                        | java.lang.Double                     | Y     |
| DOUBLE                 | YasTypes.FLOAT        | java.sql.Types.FLOAT                         | java.lang.Double                     | Y     |
| NUMBER                 | YasTypes.NUMBER       | java.sql.Types.NUMERIC                       | java.math.BigDecimal                 | Y     |
| NUMBER                 | YasTypes.DECIMAL      | java.sql.Types.DECIMAL                       | java.math.BigDecimal                 | Y     |
| RAW                    | YasTypes.BINARY       | java.sql.Types.BINARY                        | byte\[\]                             | Y     |
| RAW                    | YasTypes.VARBINARY    | java.sql.Types.VARBINARY                     | byte\[\]                             | Y     |
| DATE                   | YasTypes.DATE         | java.sql.Types.DATE                          | java.sql.Date                        | Y     |
| TIME                   | YasTypes.TIME         | java.sql.Types.TIME                          | java.sql.Time                        | Y     |
| TIMESTAMP              | YasTypes.TIMESTAMP    | java.sql.Types.TIMESTAMP                     | java.sql.Timestamp                   | Y     |
| BLOB                   | YasTypes.BLOB         | java.sql.Types.BLOB                          | byte\[\]                             | Y     |
| CLOB                   | YasTypes.CLOB         | java.sql.Types.CLOB                          | java.lang.String                     | Y     |
| STRUCT                 | YasTypes.STRUCT       | java.sql.Types.STRUCT                        | java.sql.Struct                      | N     |
| REF                    | YasTypes.REF          | java.sql.Types.REF                           | java.sql.Ref                         | N     |
| ARRAY                  | YasTypes.ARRAY        | java.sql.Types.ARRAY                         | java.sql.Array                       | N     |
| ROWID                  | YasTypes.ROWID        | java.sql.Types.ROWID                         | java.sql.RowId                       | N     |
| INTERVAL YEAR TO MONTH | YasTypes.YM_INTERVAL | java.sql.Types.OTHER                         | \--                                  | Y     |
| INTERVAL DAY TO SECOND | YasTypes.DS_INTERVAL | java.sql.Types.OTHER                         | \--                                  | Y     |
| \--                    |                       | java.sql.Types.<br>TIMESTAMP_WITH_TIMEZONE | \--                                  | Y     |
| \--                    |                       | java.sql.Types.<br>TIME_WITH_TIMEZONE      | \--                                  | Y     |
| BIT                    | YasTypes.BIT          | java.sql.Types.BIT                           | BIT(1):boolean<br>BIT(2~64):byte\[\] | Y     |
| CURSOR                 | YasTypes.CURSOR       | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   | N     |
| CURSOR                 | YasTypes.REF_CURSOR   | java.sql.Types.REF_CURSROR                   | java.sql.ResultSet                   | N     |
| JSON                   | YasTypes.JSON         | java.sql.Types.OTHER                         | \--                                  | N     |
