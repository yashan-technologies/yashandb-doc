SQLXML接口用于操作XMLTYPE类型数据。

YashanDB JDBC驱动对SQLXML接口已支持功能：

|  返回类型| 方法|
|----------------------|---------------------------------|
| void                 | free()                          |
| String               | getString()                     |
| void                 | setString(String value)         |
| InputStream          | getBinaryStream()               |
| Reader               | getCharacterStream()            |
| \<T extends Source> T | getSource(Class\<T> resultClass) |

未支持功能：

|  返回类型| 方法|
|----------------------|---------------------------------|
| OutputStream         | setBinaryStream()               |
| Writer               | setCharacterStream()            |
| \<T extends Result> T | setResult(Class\<T> resultClass) |
