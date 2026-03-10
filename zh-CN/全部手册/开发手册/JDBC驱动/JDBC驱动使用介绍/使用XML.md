SQLXML是JDBC 4.0引入的接口，用于在Java应用程序中处理XML数据。

YashanDB中的XML数据以XMLTYPE类型进行存储，YashanDB JDBC实现了一系列方法用于读取和操作YashanDB的XMLTYPE类型数据，包括：

1. 使用Connection接口的createSQLXML方法类来创建一个空的SQLXML对象。

2. 使用如下接口方法读写SQLXML对象：

   | 类| 方法|
   | -------------------------- | ------------------------------------------------------------ |
   | java.sql.ResultSet         | getSQLXML(int index)<br>getSQLXML(String columnName)         |
   | java.sql.ResultSet         | getObject(int index)<br>getObject(String columnName)         |
   | java.sql.PreparedStatement | setSQLXML(int index, SQLXML xmlObject)                       |
   | java.sql.PreparedStatement | setObject(int index,SQLXML xmlObject)<br>setObject(int index, Object x, int targetSqlType, int scale)<br>setObject(int index, Object x, int targetSqlType)<br>setObject(int index, Object x, SQLType targetSqlType,  int scaleOrLength)<br>setObject(int index, Object x, SQLType targetSqlType) |

   其中，targetSqlType为Types.SQLXML时，x可以为String,SQLXML,CLOB三种类型。

3. 使用如下接口方法读写SQLXML对象中的XML数据：

   | 类| 方法|
   | --------------- | ------------------------------- |
   | java.sql.SQLXML | getString()                     |
   | java.sql.SQLXML | setString(String value)         |
   | java.sql.SQLXML | getCharacterStream()            |
   | java.sql.SQLXML | setCharacterStream()            |
   | java.sql.SQLXML | getBinaryStream()               |
   | java.sql.SQLXML | setBinaryStream()               |
   | java.sql.SQLXML | getSource(Class\<T> sourceClass) |
   | java.sql.SQLXML | setResult(Class\<T> resultClass) |

4. 使用SQLXML接口的free方法释放SQLXML对象关联的资源。

上述读写SQLXML对象以及读写SQLXML对象中的数据的使用规则为：

- 新创建的SQLXML对象只能用于写入XML数据，不可读取

- 通过get方法返回的SQLXML对象只能用于读取XML数据，不可写入

- 一次创建或读取的SQLXML对象只可进行一次的写或读操作，之后的读写操作均无效
