SQLXML is an interface introduced in JDBC 4.0 for handling XML data in Java applications. 

XML data in YashanDB is stored as XMLTYPE type. YashanDB JDBC implements a series of methods for reading and operating on YashanDB's XMLTYPE type data, including:

1. Use the createSQLXML method of the Connection interface to create an empty SQLXML object.

2. Use the following interface methods to read and write SQLXML objects:

   |Class               |Method                                              |
   | -------------------------- | ------------------------------------------------------------ |
   | java.sql.ResultSet         | getSQLXML(int index)<br>getSQLXML(String columnName)         |
   | java.sql.ResultSet         | getObject(int index)<br>getObject(String columnName)         |
   | java.sql.PreparedStatement | setSQLXML(int index, SQLXML xmlObject)                       |
   | java.sql.PreparedStatement | setObject(int index,SQLXML xmlObject)<br>setObject(int index, Object x, int targetSqlType, int scale)<br>setObject(int index, Object x, int targetSqlType)<br>setObject(int index, Object x, SQLType targetSqlType,  int scaleOrLength)<br>setObject(int index, Object x, SQLType targetSqlType) |

   When targetSqlType is Types.SQLXML, x can be one of three types: String, SQLXML, or CLOB.

3. Use the following interface methods to read and write XML data in SQLXML objects:

   |Class    |Method                 |
   | --------------- | ------------------------------- |
   | java.sql.SQLXML | getString()                     |
   | java.sql.SQLXML | setString(String value)         |
   | java.sql.SQLXML | getCharacterStream()            |
   | java.sql.SQLXML | setCharacterStream()            |
   | java.sql.SQLXML | getBinaryStream()               |
   | java.sql.SQLXML | setBinaryStream()               |
   | java.sql.SQLXML | getSource(Class\<T> sourceClass) |
   | java.sql.SQLXML | setResult(Class\<T> resultClass) |

4. Use the free method of the SQLXML interface to release resources associated with the SQLXML object.

The usage rules for reading and writing SQLXML objects and reading and writing data within SQLXML objects are as follows:

- Newly created SQLXML objects can only be used to write XML data and cannot be read.

- SQLXML objects returned by get methods can only be used to read XML data and cannot be written to.

- A SQLXML object that is created or retrieved can only perform one write or read operation. Subsequent read/write operations are invalid.