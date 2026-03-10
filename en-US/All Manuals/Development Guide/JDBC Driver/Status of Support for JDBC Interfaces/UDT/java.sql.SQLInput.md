SQLInput is used to map UDT types from the database to user-defined Java types during query processing.

Object Methods:

|Return Type |Method |
|-------------|-----------------------------------------------|
| String      | readString() throws SQLException              |
| String      | readNString() throws SQLException             |
| boolean     | readBoolean() throws SQLException             |
| byte        | readByte() throws SQLException                |
| short       | readShort() throws SQLException               |
| int         | readInt() throws SQLException                 |
| long        | readLong()  throws SQLException               |
| float       | readFloat() throws SQLException               |
| double      | readDouble() throws SQLException              |
| BigDecimal  | readBigDecimal() throws SQLException          |
| byte[]      | readBytes() throws SQLException               |
| Date        | readDate() throws SQLException                |
| Time        | readTime() throws SQLException                |
| Timestamp   | readTimestamp() throws SQLException           |
| Blob        | readBlob() throws SQLException                |
| Clob        | readClob() throws SQLException                | 
| NClob       | readNClob() throws SQLException               |
| SQLXML      | readSQLXML() throws SQLException              |
| Reader      | readCharacterStream() throws SQLException     |
| InputStream | readAsciiStream() throws SQLException         |
| InputStream | readBinaryStream() throws SQLException        |
| Object      | readObject() throws SQLException              |
| \<T> T       | readObject(Class\<T> type) throws SQLException |
| Array       | readArray() throws SQLException               |
| RowId       | readRowId() throws SQLException               | 
| boolean     | wasNull()                                     |
Unsupported Methods:

|Return Type |Method |
|------|-------------------------------|
| Ref  | readRef() throws SQLException | 
| URL  | readURL() throws SQLException | 
