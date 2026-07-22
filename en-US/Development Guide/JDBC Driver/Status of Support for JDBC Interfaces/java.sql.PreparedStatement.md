The PreparedStatement interface extends the Statement object and is used to implement JDBC precompiled SQL statements.

Users can precompile an SQL statement and store it in a PreparedStatement object, which can then be used to efficiently execute the SQL statement multiple times.

> **Note**:
>
> In YashanDB, the SQL statement in PreparedStatement supports up to 32,000 parameters at most.

The YashanDB JDBC driver supports the following functionality for the PreparedStatement interface:

|Return Type |Method |
| --- | --- |
| void | addBatch() |
| void | clearParameters() |
| boolean | execute() |
| long | executeLargeUpdate() |
| ResultSet | executeQuery() |
| int | executeUpdate() |
| ResultSet | getGeneratedKeys() |
| ParameterMetaData | getParameterMetaData() |
| void | setArray​(int parameterIndex, Array x) |
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

The following functionality is not supported:

|Return Type |Method |
| --- | --- |
| ResultSetMetaData | getMetaData() |
| void | setNCharacterStream​(int parameterIndex, Reader value) |
| void | setNCharacterStream​(int parameterIndex, Reader value, long length) |
| void | setNClob​(int parameterIndex, Reader reader) |
| void | setNClob​(int parameterIndex, Reader reader, long length) |
| void | setNClob​(int parameterIndex, NClob value) |
| void | setRef​(int parameterIndex, Ref x) |
| void | setNull(int parameterIndex, int sqlType, String typeName) |
| void | setUnicodeStream​(int parameterIndex, InputStream x, int length) |
| void | setURL​(int parameterIndex, URL x) |

The YashanDB JDBC driver PreparedStatement extension interface functionality:

|Method |Description |
| ------------------------------------------- |-----------------------|
| void setIsBatchError(boolean batchError);    | Set whether to batch errors. If set to true, when executing multiple rows as a batch, if any row has data errors, execution will not stop immediately, but will complete all subsequent executions, and finally obtain error information of the erroneous rows through getBatchError. |
| boolean getIsBatchError();                    | Get whether to batch errors.   |
| String getBatchError(int batchIndex);        | Under the condition that setIsBatchError is set to true, obtain the error information of the erroneous rows after the batch execution ends.     |