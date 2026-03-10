SQLXML is an interface introduced in JDBC 4.0 for handling XML data in Java applications. 

The YashanDB JDBC driver supports the following functionalities for the SQLXML interface:

|Return Type |Method |Remarks |
|----------------------|---------------------------------|----------------------|
| void | free() | Release resources. No operations are allowed after free. |
| String | getString() | Read the server-side XMLTYPE and return the string representation of XML. |
| void | setString(String value) | Set the string representation value of XML. |
| InputStream | getBinaryStream() | Read the server-side XMLTYPE and return a binary stream encoded in the server's character set. |
| OutputStream | setBinaryStream() | Return a binary output stream. The output stream is used to write XML binary data, which is decoded in the server's encoding format when stored in the database. |
| Reader | getCharacterStream() | Read the server-side XMLTYPE and return a byte stream. |
| Writer | setCharacterStream() | Return a byte output stream. The output stream is used to write XML byte data. |
| \<T extends Source> T | getSource(Class\<T> sourceClass) | Read the server-side XMLTYPE and return a Source.<br>sourceClass supports the following four types:<br>javax.xml.transform.dom.DOMSource<br/>javax.xml.transform.sax.SAXSource<br/>javax.xml.transform.stax.StAXSource<br/>javax.xml.transform.stream.StreamSource |
| \<T extends Result> T | setResult(Class\<T> resultClass) | Return a Result. The Result is used to write XML values.<br>resultClass supports the following four types:<br/>javax.xml.transform.dom.DOMResult<br/>javax.xml.transform.sax.SAXResult<br/>javax.xml.transform.stax.StAXResult<br/>javax.xml.transform.stream.StreamResult |
