SQLOutput is used in the parameter binding process to map user-defined Java type objects to UDT types in the database.

Object Methods:

|Return Type |Method |Remarks |
|------|----------------------------------------------------|------------------|
| void        | writeString(String) throws SQLException          | Input type: String   |
| void        | writeNString(String) throws SQLException         | Input type: String   |
| void        | writeBoolean(boolean) throws SQLException        | Input type: boolean  |
| void        | writeByte(byte) throws SQLException              | Input type: byte     |
| void        | writeShort(short) throws SQLException            | Input type: short    |
| void        | writeInt(int) throws SQLException                | Input type: int      |
| void        | writeLong(long) throws SQLException              | Input type: long     |
| void        | writeFloat(float) throws SQLException            | Input type: float    |
| void        | writeDouble(double) throws SQLException          | Input type: double   |
| void        | writeBigDecimal(BigDecimal) throws SQLException  | Input type: BigDecimal|
| void        | writeBytes(byte[]) throws SQLException           | Input type: byte[]   |
| void        | writeDate(Date) throws SQLException              | Input type: Date     |
| void        | writeTime(Time) throws SQLException              | Input type: Time     |
| void        | writeTimestamp(TImeStamp) throws SQLException    | Input type: TImeStamp|
| void        | writeObject(Object) throws SQLException          | Input type: Object   |
| void        | writeObject(Class\<T> type) throws SQLException   | Input type: Class    |
| void        | writeArray(Array) throws SQLException            | Input type: Array    |
| void        | writeRowId(RowID) throws SQLException            | Input type: RowID    |
| void        | writeBlob(Blob) throws SQLException              | Input type: Blob     |
| void        | writeClob(Clob) throws SQLException              | Input type: Clob     |
| void        | writeNClob(NClob) throws SQLException            | Input type: NClob    |
| void        | writeSQLXML(SQLXML) throws SQLException          | Input type: SQLXML   |
| void        | writeCharacterStream(Reader) throws SQLException | Input type: Reader    |
| void        | writeAsciiStream(InputStream) throws SQLException| Input type: InputStream |
| void        | writeBinaryStream(InputStream) throws SQLException| Input type: InputStream |

Unsupported Methods:

|Return Type |Method |Remarks |
|------|-----------------------------------|-----------|
| void        | writeRef(Ref) throws SQLException   | Input type: Ref|
| void        | writeURL(URL) throws SQLException   | Input type: URL|