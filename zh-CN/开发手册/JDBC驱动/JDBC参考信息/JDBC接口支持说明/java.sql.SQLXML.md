## 描述

`java.sql.SQLXML`接口用于操作XMLTYPE类型数据，实例可通过`ResultSet.getSQLXML()`、`PreparedStatement.setSQLXML()`、`Connection.createSQLXML()`获取。核心作用是：


- 作为Java与数据库XML列之间的“数据桥梁”，支持XML数据的读写；
- 提供多种方式（流、字符串、DOM解析）访问和修改XML数据；
- 避免直接使用字符串处理XML时的编码、格式问题，保证数据完整性。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLXML.html)。

## 方法

YashanDB JDBC驱动对SQLXML接口有如下方法：

| 方法|  返回类型|
|---------------------------------|----------------------|
| free()                          | void                 |
| getString()                     | String               |
| setString(String value)         | void                 |
| getBinaryStream()               | InputStream          |
| getCharacterStream()            | Reader               |
| getSource(Class<T> resultClass) | <T extends Source> T |

## 示例

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

    // XML 查询工具
    public void searchInXMLData(Connection conn, String searchTerm)
            throws SQLException, IOException {
        String sql = "SELECT id, xml_data FROM documents WHERE xml_data LIKE ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setString(1, "%" + searchTerm + "%");

            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    int id = rs.getInt("id");
                    SQLXML sqlxml = rs.getSQLXML("xml_data");

                    // 读取XML内容
                    try (Reader reader = sqlxml.getCharacterStream()) {
                        char[] buffer = new char[1024];
                        StringBuilder content = new StringBuilder();
                        int charsRead;
                        while ((charsRead = reader.read(buffer)) != -1) {
                            content.append(buffer, 0, charsRead);
                        }

                        // 循环读取字符流数据
                        if (content.toString().contains(searchTerm)) {
                            System.out.println("Found in document ID: " + id);
                        }
                    }
                    sqlxml.free();
                }
            }
        }
    }

    // 提取 XML 片段
    public void extractXMLFragment(Connection conn, int docId, String xpath)
            throws SQLException {
        String sql = "SELECT xml_data FROM documents WHERE id = ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, docId);

            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) {
                    SQLXML sqlxml = rs.getSQLXML("xml_data");
                    String fullXML = sqlxml.getString();

                    // 注：JDBC本身不提供XPath支持，需要依赖额外第三方库
                    System.out.println("Full XML length: " + fullXML.length());

                    sqlxml.free();
                }
            }
        }
    }
}
```

