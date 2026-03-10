YashanDB ADO.NET驱动会对YashanDB数据类型和C#数据类型进行转换，转换关系如下：

- C#映射到YashanDB

  | C#数据类型      | YasTypes                   | YashanDB数据类型 |
  | --------------- | -------------------------- | ---------------- |
  | System.Boolean  | YacType.YAC_TYPE_BOOL      | BOOLEAN          |
  | System.SByte    | YacType.YAC_TYPE_TINYINT   | TINYINT          |
  | System.Int16    | YacType.YAC_TYPE_SMALLINT  | SMALLINT         |
  | System.Int32    | YacType.YAC_TYPE_INTEGER   | INTEGER          |
  | System.Int64    | YacType.YAC_TYPE_BIGINT    | BIGINT           |
  | System.Byte     | YacType.YAC_TYPE_SMALLINT  | SMALLINT         |
  | System.UInt16   | YacType.YAC_TYPE_USMALLINT | INTEGER          |
  | System.UInt32   | YacType.YAC_TYPE_UINTEGER  | BIGINT           |
  | System.UInt64   | YacType.YAC_TYPE_UBIGINT   | NUMBER           |
  | System.Single   | YacType.YAC_TYPE_FLOAT     | FLOAT            |
  | System.Double   | YacType.YAC_TYPE_DOUBLE    | DOUBLE           |
  | System.Decimal  | YacType.YAC_TYPE_NUMBER    | NUMBER           |
  | System.DateTime | YacType.YAC_TYPE_DATE      | DATE             |
  | System.Byte[]   | YacType.YAC_TYPE_BLOB      | BLOB             |
  | System.String   | YacType.YAC_TYPE_VARCHAR   | VARCHAR          |
  | System.Guid     | YacType.YAC_TYPE_CHAR      | CHAR             |

- YashanDB映射到C#

  | YashanDB数据类型 | YasTypes                     | C#数据类型      |
  | ---------------- | ---------------------------- | --------------- |
  | BOOLEAN          | YacType.YAC_TYPE_BOOL        | System.Boolean  |
  | TINYINT          | YacType.YAC_TYPE_TINYINT     | System.SByte    |
  | SMALLINT         | YacType.YAC_TYPE_SMALLINT    | System.Int16    |
  | INTEGER          | YacType.YAC_TYPE_INTEGER     | System.Int32    |
  | BIGINT           | YacType.YAC_TYPE_BIGINT      | System.Int64    |
  | FLOAT            | YacType.YAC_TYPE_FLOAT       | System.Single   |
  | DOUBLE           | YacType.YAC_TYPE_DOUBLE      | System.Double   |
  | NUMBER           | YacType.YAC_TYPE_NUMBER      | System.Decimal  |
  | DATE             | YacType.YAC_TYPE_DATE        | System.DateTime |
  | SHORTTIME        | YacType.YAC_TYPE_SHORTTIME   | System.DateTime |
  | TIMESTAMP        | YacType.YAC_TYPE_TIMESTAMP   | System.DateTime |
  | YM_INTERVAL      | YacType.YAC_TYPE_YM_INTERVAL | System.TimeSpan |
  | DS_INTERVAL      | YacType.YAC_TYPE_DS_INTERVAL | System.TimeSpan |
  | CHAR             | YacType.YAC_TYPE_CHAR        | System.String   |
  | VARCHAR          | YacType.YAC_TYPE_VARCHAR     | System.String   |
  | BINARY           | YacType.YAC_TYPE_BINARY      | System.Byte[]   |
  | CLOB             | YacType.YAC_TYPE_CLOB        | System.Byte[]   |
  | BLOB             | YacType.YAC_TYPE_BLOB        | System.Byte[]   |