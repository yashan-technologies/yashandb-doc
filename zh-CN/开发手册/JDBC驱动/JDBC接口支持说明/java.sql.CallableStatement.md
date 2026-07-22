CallableStatement接口用于实现PL对象的调用。

本接口不适用于存算一体分布式集群部署。

YashanDB JDBC驱动对CallableStatement接口已支持的功能：

|  返回类型| 方法|
|---------------------|-------------------------------------------------------|
| void                | registerOutParameter(int parameterIndex, int sqlType) |
| void | registerOutParameter(int parameterIndex, int sqlType, int scale) |
| void | registerOutParameter(String parameterName, int sqlType) |
| void | registerOutParameter(String parameterName, int sqlType, int scale) |
| void | registerOutParameter(int parameterIndex, SQLType sqlType) |
| void | registerOutParameter(int parameterIndex, SQLType sqlType, int scale) |
| void | registerOutParameter(String parameterName, SQLType sqlType) |
| void | registerOutParameter(String parameterName, SQLType sqlType, int scale) |
| void | registerOutParameter (int parameterIndex, SQLType sqlType, String typeName) |
| void | registerOutParameter (String parameterName, SQLType sqlType, String typeName) |
| void | registerOutParameter (String parameterName, int sqlType, String typeName) |
| void | registerOutParameter (int parameterIndex, int sqlType, String typeName) |
| void | setNull(String parameterName, int sqlType) |
| void | setNull(String parameterName, int sqlType, String typeName) |
| void | setBoolean(String parameterName, boolean x) |
| void | setByte(String parameterName, byte x) |
| void | setShort(String parameterName, short x) |
| void | setInt(String parameterName, int x) |
| void | setLong(String parameterName, long x) |
| void | setFloat(String parameterName, float x) |
| void | setDouble(String parameterName, double x) |
| void | setBigDecimal(String parameterName, BigDecimal x) |
| void | setString(String parameterName, String x) |
| void | setBytes(String parameterName, byte x\[\]) |
| void | setDate(String parameterName, java.sql.Date x) |
| void | setTime(String parameterName, java.sql.Time x) |
| void | setTimestamp(String parameterName, java.sql.Timestamp x) |
| void | setAsciiStream(String parameterName, java.io.InputStream x, int length) |
| void | setBinaryStream(String parameterName, java.io.InputStream x, int length) |
| void | setObject(String parameterName, Object x, int targetSqlType, int scale) |
| void | setObject(String parameterName, Object x, int targetSqlType) |
| void | setObject(String parameterName, Object x) |
| void | setObject(String parameterName, Object x, SQLType targetSqlType, int scaleOrLength) |
| void | setObject(String parameterName, Object x, SQLType targetSqlType) |
| void | setCharacterStream(String parameterName,java.io.Reader reader, int length) |
| void | setDate(String parameterName, java.sql.Date x, Calendar cal) |
| void | setTime(String parameterName, java.sql.Time x, Calendar cal) |
| void | setTimestamp(String parameterName, java.sql.Timestamp x, Calendar cal) |
| void | setNString(String parameterName, String value) |
| void | setNCharacterStream(String parameterName, Reader value, long length) |
| void | setNClob(String parameterName, NClob value) |
| void | setClob(String parameterName, Reader reader, long length) |
| void | setBlob(String parameterName, InputStream inputStream, long length) |
| void | setNClob(String parameterName, Reader reader, long length) |
| void | setSQLXML(String parameterName, SQLXML xmlObject) |
| void | setBlob (String parameterName, Blob x) |
| void | setClob (String parameterName, Clob x) |
| void | setAsciiStream(String parameterName, java.io.InputStream x, long length) |
| void | setCharacterStream(String parameterName,java.io.Reader reader,long length) |
| void | setBinaryStream(String parameterName, java.io.InputStream x, long length) |
| void | setAsciiStream(String parameterName, java.io.InputStream x) |
| void | setBinaryStream(String parameterName, java.io.InputStream x) |
| void | setCharacterStream(String parameterName,java.io.Reader reader) |
| void | setNCharacterStream(String parameterName, Reader value) |
| void | setClob(String parameterName, Reader reader) |
| void | setBlob(String parameterName, InputStream inputStream) |
| void | setNClob(String parameterName, Reader reader) |
| String | getString(String parameterName) |
| String              | getString(int parameterIndex)                         |
| String | getNString(int parameterIndex) |
| String | getNString(String parameterName) |
| boolean             | getBoolean(int parameterIndex)                        |
| byte                | getByte(int parameterIndex)                           |
| short               | getShort(int parameterIndex)                          |
| short | getShort(String parameterName) |
| int                 | getInt(int parameterIndex)                            |
| int | getInt(String parameterName) |
| long                | getLong(int parameterIndex)                           |
| long | getLong(String parameterName) |
| float               | getFloat(int parameterIndex)                          |
| float | getFloat(String parameterName) |
| double              | getDouble(int parameterIndex)                         |
| double | getDouble(String parameterName) |
| byte\[\]            | getBytes(int parameterIndex)                          |
| byte\[\] | getBytes(String parameterName) |
| byte | getByte(String parameterName) |
| java.sql.Date       | getDate(int parameterIndex)                           |
| java.sql.Date | getDate(String parameterName) |
| java.sql.Time       | getTime(int parameterIndex)                           |
| java.sql.Time | getTime(String parameterName) |
| java.sql.Timestamp  | getTimestamp(int parameterIndex)                      |
| java.sql.Timestamp | getTimestamp(String parameterName) |
| Object              | getObject(int parameterIndex)                         |
| Object | getObject(int parameterIndex, java.util.Map<String,Class<?>> map) |
| Object | getObject(String parameterName, java.util.Map<String,Class<?>> map) |
| Object | getObject(String parameterName) |
| BigDecimal          | getBigDecimal(int parameterIndex)                     |
| BigDecimal | getBigDecimal(String parameterName) |
| BigDecimal | getBigDecimal(int  parameterIndex, int  scale) |
| java.sql.Date       | getDate(int parameterIndex, Calendar cal)             |
| java.sql.Date | getDate(String parameterName, Calendar cal) |
| java.sql.Time       | getTime(int parameterIndex, Calendar cal)             |
| java.sql.Time | getTime(String parameterName, Calendar cal) |
| java.sql.Timestamp  | getTimestamp(int parameterIndex, Calendar cal)        |
| java.sql.Timestamp | getTimestamp(String parameterName, Calendar cal) |
| java.net.URL | getURL(String parameterName) |
| java.io.Reader | getNCharacterStream(int parameterIndex) |
| java.io.Reader | getNCharacterStream(String parameterName) |
| java.io.Reader | getCharacterStream(int parameterIndex) |
| java.io.Reader | getCharacterStream(String parameterName) |
| RowId               | getRowId(int parameterIndex)                          |
| RowId               | getRowId(String parameterName)                        |
| void                | setRowId(String parameterName, RowId x)               |
| Clob                | getClob (int parameterIndex)                          |
| Clob | getClob (String parameterName) |
| Blob                | getBlob (int parameterIndex)                          |
| Blob | getBlob (String parameterName) |
| ResultSet           | getGeneratedKeys()                                    |
| boolean             | wasNull()                                             |
| boolean | getBoolean(String parameterName) |
| SQLXML              | getSQLXML(int parameterIndex)                         |
| SQLXML              | getSQLXML(String parameterName)                       |
| Array | getArray (int parameterIndex) |
| Array | getArray (String parameterName) |
| NClob | getNClob (int parameterIndex) |
| NClob | getNClob (String parameterName) |
| public <T> T | getObject(int parameterIndex, Class<T> type) |
| public <T> T | getObject(String parameterName, Class<T> type) |

未支持功能：

| 返回类型  | 方法|
|-------|-----------------------------------|
| Ref   | getRef (int parameterIndex)                                                         |
| Ref   | getRef (String parameterName)                                                       |
| void  | setURL(String parameterName, java.net.URL val)                                      |