SQLXML是JDBC 4.0引入的接口，用于在Java应用程序中处理XML数据。

YashanDB JDBC驱动对SQLXML接口已支持功能：

|  返回类型| 方法| 备注|
|----------------------|---------------------------------|----------------------|
| void                 | free()                          | 释放资源，free后不允许任何操作 |
| String               | getString()                     | 读取服务端XMLTYPE并返回XML的字符串形式 |
| void                 | setString(String value)         | 设置XML的字符串形式值 |
| InputStream          | getBinaryStream()               | 读取服务端XMLTYPE并返回二进制流，以服务端的字符集进行编码 |
| OutputStream | setBinaryStream() | 返回一个二进制输出流，输出流用于写入XML二进制数据，入库时以服务端编码格式进行解码 |
| Reader               | getCharacterStream()            | 读取服务端XMLTYPE并返回字节流 |
| Writer | setCharacterStream() | 返回一个字节输出流，输出流用于写入XML字节数据 |
| <T extends Source> T | getSource(Class<T> sourceClass) | 读取服务端XMLTYPE并返回一个Source<br>sourceClass支持如下四种类型：<br>javax.xml.transform.dom.DOMSource<br>javax.xml.transform.sax.SAXSource<br>javax.xml.transform.stax.StAXSource<br>javax.xml.transform.stream.StreamSource |
| <T extends Result> T | setResult(Class<T> resultClass) | 返回一个Result，Result用于写入XML的值<br/>resultClass支持如下四种类型：<br/>javax.xml.transform.dom.DOMResult<br/>javax.xml.transform.sax.SAXResult<br/>javax.xml.transform.stax.StAXResult<br/>javax.xml.transform.stream.StreamResult |
