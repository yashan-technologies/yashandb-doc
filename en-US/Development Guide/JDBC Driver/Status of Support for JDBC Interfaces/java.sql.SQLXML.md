The SQLXML interface is used to operate XMLTYPE data.

The YashanDB JDBC driver supports the following functionalities for the SQLXML interface:

|Return Type |Method |
|----------------------|---------------------------------|
| void                 | free()                          |
| String               | getString()                     |
| void                 | setString(String value)         |
| InputStream          | getBinaryStream()               |
| Reader               | getCharacterStream()            |
| \<T extends Source> T | getSource(Class\<T> resultClass) |

The following functionalities are not supported:

|Return Type |Method |
|----------------------|---------------------------------|
| OutputStream         | setBinaryStream()               |
| Writer               | setCharacterStream()            |
| \<T extends Result> T | setResult(Class\<T> resultClass) |
