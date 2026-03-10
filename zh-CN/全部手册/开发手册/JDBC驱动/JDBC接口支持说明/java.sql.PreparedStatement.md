PreparedStatement接口继承了Statement对象，是实现JDBC预编译SQL语句的对象。

用户可将SQL语句预编译并存储在PreparedStatement对象中，之后可以使用此对象多次有效地执行SQL语句。

> **Note**:
>
> YashanDB中，PreparedStatement的SQL语句最多支持32000个参数。

YashanDB JDBC驱动对PreparedStatement接口已支持功能：

|  返回类型| 方法|
| --- | --- |
| void | addBatch() |
| void | clearParameters() |
| boolean | execute() |
| long | executeLargeUpdate() |
| ResultSet | executeQuery() |
| int | executeUpdate() |
| ResultSet | getGeneratedKeys() |
| ParameterMetaData | getParameterMetaData() |
| void | setBigDecimal​(int parameterIndex, BigDecimal x) |
| void | setBoolean​(int parameterIndex, boolean x) |
| void | setByte​(int parameterIndex, byte x) |
| void | setDate​(int parameterIndex, Date x) |
| void | setDate​(int parameterIndex, Date x, Calendar cal) |
| void | setDouble​(int parameterIndex, double x) |
| void | setFloat​(int parameterIndex, float x) |
| void | setInt​(int parameterIndex, int x) |
| void | setLong​(int parameterIndex, long x) |
| void | setBlob​(int parameterIndex, Blob x) |
| void | setBytes​(int parameterIndex, byte\[\] x) |
| void | setClob​(int parameterIndex, Clob x) |
| void | setNull​(int parameterIndex, int sqlType) |
| void | setObject​(int parameterIndex, Object x) |
| void | setObject​(int parameterIndex, Object x, int targetSqlType) |
| void | setObject​(int parameterIndex, Object x, int targetSqlType, int scaleOrLength) |
| void | setObject​(int parameterIndex, Object x, SQLType targetSqlType) |
| void | setObject​(int parameterIndex, Object x, SQLType targetSqlType, int scaleOrLength) |
| void | setRowId​(int parameterIndex, RowId x) |
| void | setShort​(int parameterIndex, short x) |
| void | setString​(int parameterIndex, String x) |
| void | setNString​(int parameterIndex, String value) |
| void | setTimestamp​(int parameterIndex, Timestamp x) |
| void | setTimestamp​(int parameterIndex, Timestamp x, Calendar cal) |
| void | setTimestamp(int i, java.time.OffsetDateTime offsetDateTime) |
| void | setTime​(int parameterIndex, Time x) |
| void | setTime​(int parameterIndex, Time x, Calendar cal) |
| void | setBinaryStream​(int parameterIndex, InputStream x) |
| void | setBinaryStream​(int parameterIndex, InputStream x, int length) |
| void | setBinaryStream​(int parameterIndex, InputStream x, long length) |
| void | setBlob​(int parameterIndex, InputStream inputStream) |
| void | setBlob​(int parameterIndex, InputStream inputStream, long length) |
| void | setClob​(int parameterIndex, Reader reader) |
| void | setClob​(int parameterIndex, Reader reader, long length) |
| void | setAsciiStream​(int parameterIndex, InputStream x) |
| void | setAsciiStream​(int parameterIndex, InputStream x, int length) |
| void | setAsciiStream​(int parameterIndex, InputStream x, long length) |
| void | setCharacterStream​(int parameterIndex, Reader reader) |
| void | setCharacterStream​(int parameterIndex, Reader reader, int length) |
| void | setCharacterStream​(int parameterIndex, Reader reader, long length) |
| void | setSQLXML​(int parameterIndex, SQLXML xmlObject) |

未支持功能：

|  返回类型| 方法|
| --- | --- |
| ResultSetMetaData | getMetaData() |
| void | setArray​(int parameterIndex, Array x) |
| void | setNCharacterStream​(int parameterIndex, Reader value) |
| void | setNCharacterStream​(int parameterIndex, Reader value, long length) |
| void | setNClob​(int parameterIndex, Reader reader) |
| void | setNClob​(int parameterIndex, Reader reader, long length) |
| void | setNClob​(int parameterIndex, NClob value) |
| void | setRef​(int parameterIndex, Ref x) |
| void | setNull(int parameterIndex, int sqlType, String typeName) |
| void | setUnicodeStream​(int parameterIndex, InputStream x, int length) |
| void | setURL​(int parameterIndex, URL x) |

YashanDB JDBC驱动 YasConnection扩展接口功能：

|  方法| 说明|
| ------------------------------------------- |-----------------------|
| void setIsBatchError(boolean batchError); | 设置是否批量报错，若设置为true，则多条数据为一批进行批量执行时，如果某一条或者某几条有数据错误时不会立即报错停止执行，而是把后面的全部执行完，最后通过getBatchError来获取报错行的报错信息。 |
| boolean getIsBatchError(); | 获取是否批量报错。   |
| String getBatchError(int batchIndex); | 在setIsBatchError设置为true的情况下在批量执行结束后获取报错行的报错信息。     |
