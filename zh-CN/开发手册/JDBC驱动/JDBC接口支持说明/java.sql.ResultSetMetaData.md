ResultSetMetadata接口用于获取结果集中列的类型和属性信息。

YashanDB JDBC驱动对ResultSetMetaData接口已支持功能：

|  返回类型| 方法|
|----------|----------------------------------|
| int      | getColumnCount()                 |
| boolean  | isAutoIncrement(int column)      |
| boolean  | isCaseSensitive(int column)      |
| boolean  | isSearchable(int column)         |
| boolean  | isCurrency(int column)           |
| int      | isNullable(int column)           |
| boolean  | isSigned(int column)             |
| String   | getColumnLabel(int column)       |
| String   | getColumnName(int column)        |
| int      | getPrecision(int column)         |
| int      | getScale(int column)             |
| int      | getColumnDisplaySize(int column) |
| int      | getColumnType(int column)        |
| String   | getColumnTypeName(int column)    |
| boolean  | isReadOnly(int column)           |
| boolean  | isWritable(int column)           |
| boolean  | isDefinitelyWritable(int column) |
| String   | getColumnClassName(int column)   |

未支持功能：

|  返回类型| 方法|
|---------|----------------------------|
| String  | getSchemaName(int column)  |
| String  | getTableName(int column)   |
| String  | getCatalogName(int column) |
