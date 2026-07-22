SQLOutput用于参数绑定过程中把用户自定义的java类型的对象映射成数据库中的UDT类型。

对象方法：

|  返回类型| 方法| 备注|
|------|----------------------------------------------------|------------------|
| void | writeString(String) throws SQLException            | 入参类型：String      |
| void | writeNString(String) throws SQLException           | 入参类型：String      |
| void | writeBoolean(boolean) throws SQLException          | 入参类型：boolean     |
| void | writeByte(byte) throws SQLException                | 入参类型：byte        |
| void | writeShort(short) throws SQLException              | 入参类型：short       |
| void | writeInt(int) throws SQLException                  | 入参类型：int         |
| void | writeLong(long) throws SQLException                | 入参类型：long        |
| void | writeFloat(float) throws SQLException              | 入参类型：float       |
| void | writeDouble(double) throws SQLException            | 入参类型：double      |
| void | writeBigDecimal(BigDecimal) throws SQLException    | 入参类型：BigDecimal  |
| void | writeBytes(byte[]) throws SQLException             | 入参类型：byte[]      |
| void | writeDate(Date) throws SQLException                | 入参类型：Date        |
| void | writeTime(Time) throws SQLException                | 入参类型：Time        |
| void | writeTimestamp(TImeStamp) throws SQLException      | 入参类型：TImeStamp   |
| void | writeObject(Object) throws SQLException            | 入参类型：Object      |
| void | writeObject(Class<T> type) throws SQLException     | 入参类型：Class       |
| void | writeArray(Array) throws SQLException              | 入参类型：Array       |
| void | writeRowId(RowID) throws SQLException              | 入参类型：RowID       | 
| void | writeBlob(Blob) throws SQLException                | 入参类型：Blob        |
| void | writeClob(Clob) throws SQLException                | 入参类型：Clob        | 
| void | writeNClob(NClob) throws SQLException              | 入参类型：NClob       |
| void | writeSQLXML(SQLXML) throws SQLException            | 入参类型：SQLXML      |
| void | writeCharacterStream(Reader) throws SQLException   | 入参类型：Reader      |
| void | writeAsciiStream(InputStream) throws SQLException  | 入参类型：InputStream |
| void | writeBinaryStream(InputStream) throws SQLException | 入参类型：InputStream |

未支持方法：

|  返回类型| 方法| 备注|
|------|-----------------------------------|-----------|
| void | writeRef(Ref) throws SQLException | 入参类型：Ref  |
| void | writeURL(URL) throws SQLException | 入参类型：URL  | 
