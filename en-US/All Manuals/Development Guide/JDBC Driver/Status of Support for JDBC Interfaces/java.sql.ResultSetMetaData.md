The ResultSetMetadata interface is used to obtain information about the types and properties of columns in a result set.

The YashanDB JDBC driver supports the following functionality for the ResultSetMetaData interface:

|Return Type |Method |
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

The following functionality is not supported:

|Return Type |Method |
|---------|----------------------------|
| String  | getSchemaName(int column)  |
| String  | getTableName(int column)   |
| String  | getCatalogName(int column) |
