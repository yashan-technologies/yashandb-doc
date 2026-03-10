ResultSet接口表示SQL查询语句执行完成后的结果集，可以通过Statement的executeQuery方法或者getResultSet方法获取。该接口用于遍历查询结果。

YashanDB JDBC驱动对ResultSet接口已支持功能：

| 返回类型                | 方法                                                                                   |
|---------------------|--------------------------------------------------------------------------------------|
| boolean             | next()                                                                               |
| void                | close()                                                                              |
| boolean             | wasNull()                                                                            |
| String              | getString(int columnIndex)                                                           |
| String              | getString(String columnLabel)                                                        |
| String              | getNString(int columnIndex)                                                          |
| String              | getNString(String columnLabel)                                                       |
| boolean             | getBoolean(int columnIndex)                                                          |
| boolean             | getBoolean(String columnLabel)                                                       |
| byte                | getByte(int columnIndex)                                                             |
| byte                | getByte(String columnLabel)                                                          |
| short               | getShort(int columnIndex)                                                            |
| short               | getShort(String columnLabel)                                                         |
| int                 | getInt(int columnIndex)                                                              |
| int                 | getInt(String columnLabel)                                                           |
| long                | getLong(int columnIndex)                                                             |
| long                | getLong(String columnLabel)                                                          |
| SQLXML              | getSQLXML(int columnIndex)                                                           |
| SQLXML              | getSQLXML(String columnLabel)                                                        |
| float               | getFloat(int columnIndex)                                                            |
| float               | float getFloat(String columnLabel)                                                   |
| double              | getDouble(int columnIndex)                                                           |
| double              | getDouble(String columnLabel)                                                        |
| BigDecimal          | getBigDecimal(int columnIndex)                                                       |
| BigDecimal          | getBigDecimal(String columnLabel)                                                    |
| BigDecimal          | getBigDecimal(int columnIndex, int scale)                                            |
| BigDecimal          | getBigDecimal(String columnLabel, int scale)                                         |
| byte\[\]            | getBytes(int columnIndex)                                                            |
| byte\[\]            | getBytes(String columnLabel)                                                         |
| java.sql.Date       | getDate(int columnIndex)                                                             |
| java.sql.Date       | getDate(String columnLabel)                                                          |
| java.sql.Date       | getDate(int columnIndex, Calendar cal)                                               |
| java.sql.Date       | getDate(String columnLabel, Calendar cal)                                            |
| java.sql.Timestamp  | getTimestamp(int columnIndex)                                                        |
| java.sql.Timestamp  | getTimestamp(String columnLabel)                                                     |
| java.sql.Timestamp  | getTimestamp(int columnIndex, Calendar cal)                                          |
| java.sql.Timestamp  | getTimestamp(String columnLabel, Calendar cal)                                       |
| java.io.InputStream | getAsciiStream(int columnIndex)                                                      |
| java.io.InputStream | getAsciiStream(String columnLabel)                                                   |
| java.io.InputStream | getBinaryStream(int columnIndex)                                                     |
| java.io.InputStream | getBinaryStream(String columnLabel)                                                  |
| java.io.Reader      | getCharacterStream(int columnIndex)                                                  |
| java.io.Reader      | getCharacterStream(String columnLabel)                                               |
| ResultSetMetaData   | getMetaData()                                                                        |
| Object              | getObject(int columnIndex)                                                           |
| Object              | getObject(String columnLabel)                                                        |
| int                 | findColumn(String columnLabel)                                                       |
| boolean             | isFirst()                                                                            |
| int                 | getRow()                                                                             |
| int                 | getType()                                                                            |
| int                 | getConcurrency()                                                                     |
| Statement           | getStatement()                                                                       |
| RowId               | getRowId(int columnIndex)                                                            |
| RowId               | getRowId(String columnLabel)                                                         |
| boolean             | isClosed()                                                                           |
| java.sql.Time       | getTime(int columnIndex)                                                             |
| java.sql.Time       | getTime(String columnLabel)                                                          |
| java.sql.Time       | getTime(int columnIndex, Calendar cal)                                               |
| java.sql.Time       | getTime(String columnLabel, Calendar cal)                                            |
| Blob                | getBlob(int columnIndex)                                                             |
| Blob                | getBlob(String columnLabel)                                                          |
| Clob                | getClob(int columnIndex)                                                             |
| Clob                | getClob(String columnLabel)                                                          |
| \<T> T               | getObject(int columnIndex, Class\<T> type)                                            |
| \<T> T               | getObject(String columnLabel, Class\<T> type)                                         |
| Object              | getObject(int columnIndex, java.util.Map<String,Class\<?>> map)                       |
| Object              | getObject(String columnLabel, java.util.Map<String,Class\<?>> map)                    |
| Array               | getArray(int columnIndex)                                                            |
| Array               | getArray(String columnLabel)                                                         |
| boolean             | isBeforeFirst()                                                                      |
| boolean             | isAfterLast()                                                                        |
| boolean             | isLast()                                                                             |
| void                | beforeFirst()                                                                        |
| void                | afterLast()                                                                          |
| boolean             | first()                                                                              |
| boolean             | last()                                                                               |
| boolean             | absolute( int row )                                                                  |
| boolean             | relative( int rows )                                                                 |
| boolean             | previous()                                                                           |
| boolean             | rowUpdated()                                                                         |
| boolean             | rowInserted()                                                                        |
| boolean             | rowDeleted()                                                                         |
| void                | updateNull(int columnIndex)                                                          |
| void                | updateNull(String columnLabel)                                                       |
| void                | updateByte(int columnIndex, byte x)                                                  |
| void                | updateByte(String columnLabel, byte x)                                               |
| void                | updateShort(int columnIndex, short x)                                                |
| void                | updateShort(String columnLabel, short x)                                             |
| void                | updateInt(int columnIndex, int x)                                                    |
| void                | updateInt(String columnLabel, int x)                                                 |
| void                | updateLong(int columnIndex, long x)                                                  |
| void                | updateLong(String columnLabel, long x)                                               |
| void                | updateFloat(int columnIndex, float x)                                                |
| void                | updateFloat(String columnLabel, float x)                                             |
| void                | updateDouble(int columnIndex, double x)                                              |
| void                | updateDouble(String columnLabel, double x)                                           |
| void                | updateBigDecimal(int columnIndex, BigDecimal x)                                      |
| void                | updateBigDecimal(String columnLabel, BigDecimal x)                                   |
| void                | updateString(int columnIndex, String x)                                              |
| void                | updateString(String columnLabel, String x)                                           |
| void                | updateSQLXML(int columnIndex, SQLXML xmlObject)                                      |
| void                | updateSQLXML(String columnLabel, SQLXML xmlObject)                                   |
| void                | updateDate(int columnIndex, java.sql.Date x)                                         |
| void                | updateDate(String columnLabel, java.sql.Date x)                                      |
| void                | updateTime(int columnIndex, java.sql.Time x)                                         |
| void                | updateTime(String columnLabel, java.sql.Time x)                                      |
| void                | updateTimestamp(int columnIndex, java.sql.Timestamp x)                               |
| void                | updateTimestamp(String columnLabel, java.sql.Timestamp x)                            |
| void                | updateBoolean(String columnLabel, boolean x)                                         |
| void                | updateObject(int columnIndex, Object x, int scaleOrLength)                           |
| void                | updateObject(int columnIndex, Object x)                                              |
| void                | updateObject(String columnLabel, Object x, int scaleOrLength)                        |
| void                | updateObject(String columnLabel, Object x)                                           |
| void                | updateObject(int columnIndex, Object x, SQLType targetSqlType, int scaleOrLength)    |
| void                | updateObject(String columnLabel, Object x, SQLType targetSqlType, int scaleOrLength) |
| void                | updateObject(int columnIndex, Object x, SQLType targetSqlType)                       |
| void                | updateObject(String columnLabel, Object x, SQLType targetSqlType)                    |
| void                | insertRow()                                                                          |
| void                | updateRow()                                                                          |
| void                | deleteRow()                                                                          |
| void                | updateBytes(int columnIndex, byte x\[\])                                             |
| void                | updateBytes(String columnLabel, byte x\[\])                                          |
| void                | updateRowId(int columnIndex, RowId x)                                                |
| void                | updateRowId(String columnLabel, RowId x)                                             |
| void                | moveToInsertRow()                                                                    |
| void                | moveToCurrentRow()                                                                   |
| void                | refreshRow()                                                                         |

未支持功能：

| 返回类型                | 方法                                                                             |
|---------------------|--------------------------------------------------------------------------------|
| java.net.URL        | getURL(int columnIndex)                                                        |
| java.net.URL        | getURL(String columnLabel)                                                     |
| java.io.InputStream | getUnicodeStream(int columnIndex)                                              |
| java.io.InputStream | getUnicodeStream(String columnLabel)                                           |
| java.io.Reader      | getNCharacterStream(int columnIndex)                                           |
| java.io.Reader      | getNCharacterStream(String columnLabel)                                        |
| SQLWarning          | getWarnings()                                                                  |
| void                | clearWarnings()                                                                |
| String              | getCursorName()                                                                |
| void                | setFetchDirection(int direction)                                               |
| int                 | getFetchDirection()                                                            |
| void                | setFetchSize(int rows)                                                         |
| int                 | getFetchSize()                                                                 |
| void                | updateArray(int columnIndex, java.sql.Array x)                                 |
| void                | updateArray(String columnLabel, java.sql.Array x)                              |
| void                | updateRef(int columnIndex, java.sql.Ref x)                                     |
| void                | updateRef(String columnLabel, java.sql.Ref x)                                  |
| void                | updateBlob(int columnIndex, java.sql.Blob x)                                   |
| void                | updateBlob(String columnLabel, java.sql.Blob x)                                |
| void                | updateBlob(int columnIndex, InputStream inputStream, long length)              |
| void                | updateBlob(String columnLabel, InputStream inputStream, long length)           |
| void                | updateBlob(int columnIndex, InputStream inputStream)                           |
| void                | updateBlob(String columnLabel, InputStream inputStream)                        |
| void                | updateClob(int columnIndex, java.sql.Clob x)                                   |
| void                | updateClob(int columnIndex, Reader reader)                                     |
| void                | updateClob(String columnLabel, Reader reader)                                  |
| void                | updateClob(String columnLabel, java.sql.Clob x)                                |
| void                | updateClob(int columnIndex, Reader reader, long length)                        |
| void                | updateClob(String columnLabel, Reader reader, long length)                     |
| void                | updateNClob(int columnIndex, NClob nClob)                                      |
| void                | updateNClob(String columnLabel, NClob nClob)                                   |
| void                | updateNClob(int columnIndex, Reader reader, long length)                       |
| void                | updateNClob(String columnLabel, Reader reader, long length)                    |
| void                | updateNClob(int columnIndex, Reader reader)                                    |
| void                | updateNClob(String columnLabel, Reader reader)                                 |
| void                | updateCharacterStream(int columnIndex, java.io.Reader x, int length)           |
| void                | updateCharacterStream(String columnLabel, java.io.Reader reader, int length)   |
| void                | updateCharacterStream(int columnIndex, java.io.Reader x, long length)          |
| void                | updateCharacterStream(String columnLabel,java.io.Reader reader,long length)    |
| void                | updateCharacterStream(int columnIndex, java.io.Reader x)                       |
| void                | updateCharacterStream(String columnLabel, java.io.Reader reader)               |
| void                | updateNCharacterStream(int columnIndex, java.io.Reader x)                      |
| void                | updateNCharacterStream(int columnIndex, java.io.Reader x, long length)         |
| void                | updateNCharacterStream(String columnLabel, java.io.Reader reader, long length) |
| void                | updateNCharacterStream(String columnLabel, java.io.Reader reader)              |
| void                | updateAsciiStream(int columnIndex, java.io.InputStream x, long length)         |
| void                | updateAsciiStream(String columnLabel, java.io.InputStream x, long length)      |
| void                | updateAsciiStream(int columnIndex, java.io.InputStream x)                      |
| void                | updateBinaryStream(int columnIndex, java.io.InputStream x, long length)        |
| void                | updateAsciiStream(String columnLabel, java.io.InputStream x)                   |
| void                | updateBinaryStream(int columnIndex, java.io.InputStream x, int length)         |
| void                | updateBinaryStream(String columnLabel, java.io.InputStream x, int length)      |
| void                | updateBinaryStream(String columnLabel,java.io.InputStream x,long length)       |
| void                | updateBinaryStream(int columnIndex, java.io.InputStream x)                     |
| void                | updateBinaryStream(String columnLabel, java.io.InputStream x)                  |
| void                | updateNString(int columnIndex, String nString)                                 |
| void                | updateNString(String columnLabel, String nString)                              |
| void                | cancelRowUpdates()                                                             |
| Ref                 | getRef(int columnIndex)                                                        |
| Ref                 | getRef(String columnLabel)                                                     |
| NClob               | getNClob(int columnIndex)                                                      |
| NClob               | getNClob(String columnLabel)                                                   |
| int                 | getHoldability()                                                               |