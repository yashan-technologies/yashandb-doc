## Description

`java.sql.SQLXML` represents that it is used to manipulate XMLTYPE data. Instances can be obtained via `ResultSet.getSQLXML()` (reading), `PreparedStatement.setSQLXML()` (writing), and `Connection.createSQLXML()` (creation). Its core functions are as follows:


- Acts as a data bridge between Java and XML columns in databases, supporting the reading and writing of XML data;
- Provides multiple methods (streams, strings, DOM parsing) to access and modify XML data;
- Avoids encoding and formatting issues that arise when processing XML directly with strings, ensuring data integrity.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLXML.html).

## Method

The YashanDB JDBC driver has the following methods for the SQLXML interface:

|Method |Return Type |
|---------------------------------|----------------------|
| free()                          | void                 |
| getString()                     | String               |
| setString(String value)         | void                 |
| getBinaryStream()               | InputStream          |
| getCharacterStream()            | Reader               |
| getSource(Class<T> resultClass) | <T extends Source> T |

## Example

```java
package quickstart;

import java.io.IOException;
import java.io.Reader;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.SQLXML;
import java.util.Properties;

public class XMLQueryTool {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // XML Query Tool
    public void searchInXMLData(Connection conn, String searchTerm)
            throws SQLException, IOException {
        String sql = "SELECT id, xml_data FROM documents WHERE xml_data LIKE ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setString(1, "%" + searchTerm + "%");

            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    int id = rs.getInt("id");
                    SQLXML sqlxml = rs.getSQLXML("xml_data");

                    // Read XML content
                    try (Reader reader = sqlxml.getCharacterStream()) {
                        char[] buffer = new char[1024];
                        StringBuilder content = new StringBuilder();
                        int charsRead;
                        while ((charsRead = reader.read(buffer)) != -1) {
                            content.append(buffer, 0, charsRead);
                        }

                        // Check if contains search term
                        if (content.toString().contains(searchTerm)) {
                            System.out.println("Found in document ID: " + id);
                        }
                    }
                    sqlxml.free();
                }
            }
        }
    }

    // Extract XML fragment
    public void extractXMLFragment(Connection conn, int docId, String xpath)
            throws SQLException {
        String sql = "SELECT xml_data FROM documents WHERE id = ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, docId);

            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) {
                    SQLXML sqlxml = rs.getSQLXML("xml_data");
                    String fullXML = sqlxml.getString();

                    // Note: JDBC itself doesn't provide XPath support, requires additional libraries
                    System.out.println("Full XML length: " + fullXML.length());

                    sqlxml.free();
                }
            }
        }
    }
}
```

