The CallableStatement interface is used to implement the invocation of PL objects.

This interface is not suitable for ISC Distributed Cluster Deployment.

The YashanDB JDBC driver supports the following functionalities of the CallableStatement interface:

|Return Type |Method |
|---------------------|-------------------------------------------------------|
| void                | registerOutParameter(int parameterIndex, int sqlType) |
| String              | getString(int parameterIndex)                         |
| boolean             | getBoolean(int parameterIndex)                        |
| byte                | getByte(int parameterIndex)                           |
| short               | getShort(int parameterIndex)                          |
| int                 | getInt(int parameterIndex)                            |
| long                | getLong(int parameterIndex)                           |
| float               | getFloat(int parameterIndex)                          |
| double              | getDouble(int parameterIndex)                         |
| byte\[\]            | getBytes(int parameterIndex)                          |
| java.sql.Date       | getDate(int parameterIndex)                           |
| java.sql.Time       | getTime(int parameterIndex)                           |
| java.sql.Timestamp  | getTimestamp(int parameterIndex)                      |
| Object              | getObject(int parameterIndex)                         |
| BigDecimal          | getBigDecimal(int parameterIndex)                     |
| java.sql.Date       | getDate(int parameterIndex, Calendar cal)             |
| java.sql.Time       | getTime(int parameterIndex, Calendar cal)             |
| java.sql.Timestamp  | getTimestamp(int parameterIndex, Calendar cal)        |
| RowId               | getRowId(int parameterIndex)                          |
| RowId               | getRowId(String parameterName)                        |
| void                | setRowId(String parameterName, RowId x)               |
| Clob                | getClob (int parameterIndex)                          |
| Blob                | getBlob (int parameterIndex)                          |
| ResultSet           | getGeneratedKeys()                                    |
| boolean             | wasNull()                                             |
| SQLXML              | getSQLXML(int parameterIndex)                         |
| SQLXML              | getSQLXML(String parameterName)                       |

Unsupported functionalities:

|Return Type |Method |
|-----------------------|-----------------------------------|
| void                  | registerOutParameter(int parameterIndex, int sqlType, int scale)                    |
| void                  | registerOutParameter(int parameterIndex, SQLType sqlType)                           |
| void                  | registerOutParameter(int parameterIndex, SQLType sqlType, int scale)                |
| void                  | registerOutParameter (int parameterIndex, SQLType sqlType, String typeName)         |
| void                  | registerOutParameter(String parameterName, SQLType sqlType)                         |
| void                  | registerOutParameter(String parameterName, SQLType sqlType, int scale)              |
| void                  | registerOutParameter (String parameterName, SQLType sqlType, String typeName)       |
| BigDecimal            | getBigDecimal(int  parameterIndex, int  scale)  throws  SQLException;               |
| Object                | getObject(int parameterIndex, java.util.Map<String,Class\<?>> map)                   |
| Ref                   | getRef (int parameterIndex)                                                         |
| Array                 | getArray (int parameterIndex)                                                       |
| void                  | registerOutParameter (int parameterIndex, int sqlType, String typeName)             |
| void                  | registerOutParameter(String parameterName, int sqlType)                             |
| void                  | registerOutParameter(String parameterName, int sqlType, int scale)                  |
| void                  | registerOutParameter (String parameterName, int sqlType, String typeName)           |
| java.net.URL          | getURL(int parameterIndex)                                                          |
| String                | getString(String parameterName)                                                     |
| boolean               | getBoolean(String parameterName)                                                    |
| byte                  | getByte(String parameterName)                                                       |
| short                 | getShort(String parameterName)                                                      |
| int                   | getInt(String parameterName)                                                        |
| long                  | getLong(String parameterName)                                                       |
| float                 | getFloat(String parameterName)                                                      |
| double                | getDouble(String parameterName)                                                     |
| byte\[\]              | getBytes(String parameterName)                                                      |
| java.sql.Date         | getDate(String parameterName)                                                       |
| java.sql.Time         | getTime(String parameterName)                                                       |
| java.sql.Timestamp    | getTimestamp(String parameterName)                                                  |
| Object                | getObject(String parameterName)                                                     |
| BigDecimal            | getBigDecimal(String parameterName)                                                 |
| Object                | getObject(String parameterName, java.util.Map<String,Class\<?>> map)                 |
| Ref                   | getRef (String parameterName)                                                       |
| Blob                  | getBlob (String parameterName)                                                      |
| Clob                  | getClob (String parameterName)                                                      |
| Array                 | getArray (String parameterName)                                                     |
| java.sql.Date         | getDate(String parameterName, Calendar cal)                                         |
| java.sql.Time         | getTime(String parameterName, Calendar cal)                                         |
| java.sql.Timestamp    | getTimestamp(String parameterName, Calendar cal)                                    |
| java.net.URL          | getURL(String parameterName)                                                        |
| NClob                 | getNClob (int parameterIndex)                                                       |
| NClob                 | getNClob (String parameterName)                                                     |
| String                | getNString(int parameterIndex)                                                      |
| String                | getNString(String parameterName)                                                    |
| java.io.Reader        | getNCharacterStream(int parameterIndex)                                             |
| java.io.Reader        | getNCharacterStream(String parameterName)                                           |
| java.io.Reader        | getCharacterStream(int parameterIndex)                                              |
| java.io.Reader        | getCharacterStream(String parameterName)                                            |
| public \<T> T          | getObject(int parameterIndex, Class\<T> type)                                        |
| public \<T> T          | getObject(String parameterName, Class\<T> type)                                      |
| void                  | setObject(String parameterName, Object x, SQLType targetSqlType, int scaleOrLength) |
| void                  | setObject(String parameterName, Object x, SQLType targetSqlType)                    |
| void                  | setURL(String parameterName, java.net.URL val)                                      |
| void                  | setNull(String parameterName, int sqlType)                                          |
| void                  | setBoolean(String parameterName, boolean x)                                         |
| void                  | setByte(String parameterName, byte x)                                               |
| void                  | setShort(String parameterName, short x)                                             |
| void                  | setInt(String parameterName, int x)                                                 |
| void                  | setLong(String parameterName, long x)                                               |
| void                  | setFloat(String parameterName, float x)                                             |
| void                  | setDouble(String parameterName, double x)                                           |
| void                  | setBigDecimal(String parameterName, BigDecimal x)                                   |
| void                  | setString(String parameterName, String x)                                           |
| void                  | setBytes(String parameterName, byte x\[\])                                          |
| void                  | setDate(String parameterName, java.sql.Date x)                                      |
| void                  | setTime(String parameterName, java.sql.Time x)                                      |
| void                  | setTimestamp(String parameterName, java.sql.Timestamp x)                            |
| void                  | setAsciiStream(String parameterName, java.io.InputStream x, int length)             |
| void                  | setBinaryStream(String parameterName, java.io.InputStream x, int length)            |
| void                  | setObject(String parameterName, Object x, int targetSqlType, int scale)             |
| void                  | setObject(String parameterName, Object x, int targetSqlType)                        |
| void                  | setObject(String parameterName, Object x)                                           |
| void                  | setCharacterStream(String parameterName,java.io.Reader reader, int length)          |
| void                  | setDate(String parameterName, java.sql.Date x, Calendar cal)                        |
| void                  | setTime(String parameterName, java.sql.Time x, Calendar cal)                        |
| void                  | setTimestamp(String parameterName, java.sql.Timestamp x, Calendar cal)              |
| void                  | setNull (String parameterName, int sqlType, String typeName)                        |
| void                  | setNString(String parameterName, String value)                                      |
| void                  | setNCharacterStream(String parameterName, Reader value, long length)                |
| void                  | setNClob(String parameterName, NClob value)                                         |
| void                  | setClob(String parameterName, Reader reader, long length)                           |
| void                  | setBlob(String parameterName, InputStream inputStream, long length)                 |
| void                  | setNClob(String parameterName, Reader reader, long length)                          |
| void                  | setSQLXML(String parameterName, SQLXML xmlObject)                                   |
| void                  | setBlob (String parameterName, Blob x)                                              |
| void                  | setClob (String parameterName, Clob x)                                              |
| void                  | setAsciiStream(String parameterName, java.io.InputStream x, long length)            |
| void                  | setBinaryStream(String parameterName, java.io.InputStream x, long length)           |
| void                  | setCharacterStream(String parameterName,java.io.Reader reader,long length)          |
| void                  | setAsciiStream(String parameterName, java.io.InputStream x)                         |
| void                  | setBinaryStream(String parameterName, java.io.InputStream x)                        |
| void                  | setCharacterStream(String parameterName,java.io.Reader reader)                      |
| void                  | setNCharacterStream(String parameterName, Reader value)                             |
| void                  | setClob(String parameterName, Reader reader)                                        |
| void                  | setBlob(String parameterName, InputStream inputStream)                              |
| void                  | setNClob(String parameterName, Reader reader)                                       |
