## 描述

Clob接口用于操作CLOB类型数据。

`java.sql.Clob`是JDBC规范中专门用于操作数据库大字符对象的核心接口，它是Java程序与数据库中`CLOB`类型字段的交互桥梁。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Clob.html)。

## 方法

YashanDB JDBC驱动对CLOB接口有如下方法（单机部署/共享集群/分布式集群部署）：

| 方法|  返回类型|
|------------------------------------------------------|----------------------|
| length()                                             | long                 |
| getSubString(long pos, int length)                   | String               |
| setString(long pos, String str)                      | int                  |
| setString(long pos, String str, int offset, int len) | int                  |
| setAsciiStream(long pos)                             | java.io.OutputStream |
| setCharacterStream(long pos)                         | java.io.Writer       |
| getCharacterStream()                                 | java.io.Reader       |
| getCharacterStream(long pos, long length)            | Reader               |
| getAsciiStream()                                     | java.io.InputStream  |
| free()                                               | void                 |
| truncate(long len)                                   | void                 |

YashanDB JDBC驱动对CLOB接口有如下方法（存算一体分布式集群部署）：

| 方法|  返回类型| 备注|
|------------------------------------------------------|----------------------|------------------------------------------------|
| length()                                             | long                 |  -                                              |
| getSubString(long pos, int length)                   | String               | -                                               |
| setString(long pos, String str)                      | int                  | -                                               |
| setString(long pos, String str, int offset, int len) | int                  | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| getCharacterStream()                                 | java.io.Reader       | -                                               |
| getAsciiStream()                                     | java.io.InputStream  | -                                               |
| setAsciiStream(long pos)                             | java.io.OutputStream | -                                               |
| setCharacterStream(long pos)                         | java.io.Writer       | -                                               |
| getCharacterStream(long pos, long length)            | Reader               | -                                               |
| truncate(long len)                                   | void                 | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| free()                                               | void                 | -                                               |

## 示例

```java
package quickstart;

import java.io.*;
import java.sql.*;
import java.util.*;

public class ClobCompleteExample {

    // 创建数据库连接
    public static Connection getConnection() throws SQLException {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        try {
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return DriverManager.getConnection(sourceURL, user, password);
    }

    public static void main(String[] args) {
        ClobCompleteExample example = new ClobCompleteExample();

        try (Connection conn = getConnection()) {
            System.out.println("=== CLOB Interface Complete Demonstration ===");

            // 1. 创建并初始化CLOB
            Clob clob = example.createAndInitializeClob(conn);

            // 2. 演示所有Clob方法
            example.demonstrateAllClobMethods(conn, clob);

            // 3. 数据库相关操作
            example.databaseClobOperations(conn);

            // 4. 实际业务场景
            example.realWorldScenarios(conn);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 创建并初始化带示例数据的CLOB
    public Clob createAndInitializeClob(Connection conn) throws SQLException {
        System.out.println("1. Creating and initializing CLOB...");

        // 创建空CLOB
        Clob clob = conn.createClob();

        // 方法1: setString - 设置完整内容
        String initialContent = "This is the initial CLOB content.\n" +
                               "CLOB stands for Character Large Object.\n" +
                               "It's used to store large text data in databases.";

        clob.setString(1, initialContent);
        System.out.println("   Initial CLOB created with " + clob.length() + " characters");

        return clob;
    }
    
    // 演示所有Clob接口方法
    public void demonstrateAllClobMethods(Connection conn, Clob clob)
            throws SQLException, IOException {
        System.out.println("2. Demonstrating all Clob methods:");

        // length() - 获取 CLOB 长度
        long length = clob.length();
        System.out.println("    length(): " + length + " characters");

        // getSubString() - 读取 CLOB 部分内容
        String substring = clob.getSubString(1, 20);
        System.out.println("    getSubString(1, 20): \"" + substring + "\"");

        // 带偏移量的 setString() - 修改 CLOB 内容
        String newText = "Updated text using setString with offset.";
        clob.setString(30, newText, 0, 10);
        System.out.println("    setString() with offset completed");

        // getCharacterStream() - 以字符流形式读取
        try (Reader reader = clob.getCharacterStream()) {
            char[] buffer = new char[100];
            int charsRead = reader.read(buffer);
            System.out.println("    getCharacterStream() read " + charsRead + " chars");
        }

        // 指定范围的 getCharacterStream() - 分段字符流读取
        try (Reader reader = clob.getCharacterStream(10, 15)) {
            char[] buffer = new char[15];
            int charsRead = reader.read(buffer);
            System.out.println("    getCharacterStream(10, 15) read " + charsRead + " chars");
        }

        // getAsciiStream() - 以 ASCII 字节流形式读取
        try (InputStream is = clob.getAsciiStream()) {
            byte[] buffer = new byte[100];
            int bytesRead = is.read(buffer);
            System.out.println("    getAsciiStream() read " + bytesRead + " bytes");
        }

        // setCharacterStream() - 通过字符流写入数据
        Clob newClob = conn.createClob();
        try (Writer writer = newClob.setCharacterStream(1)) {
            writer.write("Content written via setCharacterStream.");
        }
        System.out.println("    setCharacterStream() wrote to new CLOB");

        // setAsciiStream() - 通过 ASCII 字节流写入数据
        Clob asciiClob = conn.createClob();
        try (OutputStream os = asciiClob.setAsciiStream(1)) {
            os.write("ASCII content".getBytes("US-ASCII"));
        }
        System.out.println("    setAsciiStream() wrote ASCII content");

        // truncate() - 截断 CLOB 内容
        clob.truncate(50);
        System.out.println("    truncate(50): CLOB now " + clob.length() + " chars");

        // free() - 释放资源
        asciiClob.free();
        newClob.free();
        System.out.println("    free() released temporary CLOB resources");

        System.out.println();
    }
    

    // CLOB数据库操作
    public void databaseClobOperations(Connection conn) throws SQLException, IOException {
        System.out.println("3. Database CLOB Operations:");


        // 创建演示表
        createClobTable(conn);

        // 存储CLOB到数据库
        Clob articleClob = conn.createClob();
        articleClob.setString(1, "This is a long article about JDBC and CLOB interfaces...");

        String insertSQL = "INSERT INTO clob_demo (id, title, content, author) VALUES (clob_demo_seq.NEXTVAL, ?, ?, ?)";
        try (PreparedStatement ps = conn.prepareStatement(insertSQL)) {
            ps.setString(1, "JDBC CLOB Tutorial");
            ps.setClob(2, articleClob);
            ps.setString(3, "Admin");
            ps.executeUpdate();

            System.out.println("    CLOB stored in database");

            // 清理
            articleClob.free();
        }

        // 检索和处理CLOB
        String selectSQL = "SELECT id, title, content, LENGTH(content) as content_length " +
                          "FROM clob_demo WHERE title = ?";
        try (PreparedStatement ps = conn.prepareStatement(selectSQL)) {
            ps.setString(1, "JDBC CLOB Tutorial");

            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) {
                    int id = rs.getInt("id");
                    String title = rs.getString("title");
                    Clob storedClob = rs.getClob("content");
                    long contentLength = rs.getLong("content_length");

                    System.out.println("    Retrieved CLOB:");
                    System.out.println("      - ID: " + id);
                    System.out.println("      - Title: " + title);
                    System.out.println("      - Content length: " + contentLength + " characters");

                    // 读取前50个字符
                    String preview = storedClob.getSubString(1, 50);
                    System.out.println("      - Preview: " + preview + "...");
                    // 清理
                    storedClob.free();
                }
            }
        }

        // 更新CLOB在数据库中
        Clob updatedClob = conn.createClob();
        updatedClob.setString(1, "Updated article content with more details...");

        String updateSQL = "UPDATE clob_demo SET content = ? WHERE title = ?";
        try (PreparedStatement ps = conn.prepareStatement(updateSQL)) {
            ps.setClob(1, updatedClob);
            ps.setString(2, "JDBC CLOB Tutorial");
            int rowsUpdated = ps.executeUpdate();

            System.out.println("    Updated " + rowsUpdated + " row(s) with new CLOB");
            // 清理
            updatedClob.free();
        }

        System.out.println();
    }
    
    // CLOB实际使用场景
    public void realWorldScenarios(Connection conn) throws Exception {
        System.out.println("4. Real-world CLOB Scenarios:");

        // 场景1: 大型文本文档存储
        System.out.println("   Scenario 1: Large Document Storage");

        Clob document = conn.createClob();
        StringBuilder largeContent = new StringBuilder();
        for (int i = 1; i <= 1000; i++) {
            largeContent.append("Line ").append(i).append(": Sample text for CLOB storage.\n");
        }

        document.setString(1, largeContent.toString());
        System.out.println("      - Created document with " + document.length() + " characters");
        document.free();

        // 场景2: 日志文件存储
        System.out.println("   Scenario 2: Log File Storage");

        Clob logClob = conn.createClob();
        try (Writer writer = logClob.setCharacterStream(1)) {
            for (int i = 0; i < 100; i++) {
                writer.write(String.format("[%s] INFO: Application event %d\n",
                    new java.util.Date(), i));
            }
        }
        System.out.println("      - Stored " + logClob.length() + " characters of log data");
        logClob.free();

        // 场景3: XML/JSON数据存储
        System.out.println("   Scenario 3: XML/JSON Data Storage");

        String jsonData = "{\"user\": {\"id\": 1, \"name\": \"John\", \"preferences\": " +
                         "{\"theme\": \"dark\", \"language\": \"en\"}}}";

        Clob jsonClob = conn.createClob();
        jsonClob.setString(1, jsonData);

        // 提取特定部分
        String userName = jsonClob.getSubString(
            jsonData.indexOf("\"name\": \"") + 9,
            "John".length()
        );
        System.out.println("      - Stored JSON, extracted name: " + userName);
        jsonClob.free();

        // 场景4: 邮件/内容管理
        System.out.println("   Scenario 4: Email Content Management");

        Clob emailClob = conn.createClob();
        String emailContent = "Subject: CLOB Interface Demo\n" +
                            "From: sender@example.com\n" +
                            "To: recipient@example.com\n\n" +
                            "Dear User,\n\n" +
                            "This email demonstrates CLOB usage for storing email content.\n" +
                            "CLOBs are perfect for storing large text content like emails.\n\n" +
                            "Best regards,\nJDBC Team";

        emailClob.setString(1, emailContent);

        // 统计邮件行数
        int lineCount = 0;
        try (BufferedReader reader = new BufferedReader(
                emailClob.getCharacterStream())) {
            while (reader.readLine() != null) {
                lineCount++;
            }
        }
        System.out.println("      - Email stored with " + lineCount + " lines");
        emailClob.free();

        System.out.println();
    }
    
    // 创建演示表
    private void createClobTable(Connection conn) throws SQLException {
        String dropSQL = "DROP TABLE IF EXISTS clob_demo";
        String createSQL = "CREATE TABLE clob_demo (" +
                          "id INT PRIMARY KEY," +
                          "title VARCHAR(255) NOT NULL," +
                          "content CLOB," +
                          "author VARCHAR(100)," +
                          "created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)";

        try (Statement stmt = conn.createStatement()) {
            try {
                stmt.execute(dropSQL);
            } catch (SQLException e) {
                // 表可能不存在，忽略该错误
            }
            stmt.execute(createSQL);
            try {
                stmt.execute("CREATE SEQUENCE clob_demo_seq START WITH 1 INCREMENT BY 1");
            } catch (SQLException e) {
                // 序列可能不存在，忽略该错误
            }
        }
    }

    // 工具方法：安全地将CLOB转换为String
    public String clobToString(Clob clob) throws SQLException, IOException {
        if (clob == null) {
            return null;
        }

        StringBuilder sb = new StringBuilder();
        try (Reader reader = clob.getCharacterStream()) {
            char[] buffer = new char[4096];
            int charsRead;
            while ((charsRead = reader.read(buffer)) != -1) {
                sb.append(buffer, 0, charsRead);
            }
        }
        return sb.toString();
    }

    // 工具方法：从文件创建CLOB
    public Clob createClobFromFile(Connection conn, File file)
            throws SQLException, IOException {
        Clob clob = conn.createClob();
        try (Reader fileReader = new FileReader(file);
             Writer clobWriter = clob.setCharacterStream(1)) {
            char[] buffer = new char[8192];
            int charsRead;
            while ((charsRead = fileReader.read(buffer)) != -1) {
                clobWriter.write(buffer, 0, charsRead);
            }
        }
        return clob;
    }

    // 工具方法：向现有CLOB追加文本
    public void appendToClob(Clob clob, String text) throws SQLException {
        long currentLength = clob.length();
        clob.setString(currentLength + 1, text);
    }

    // 工具方法：在CLOB中搜索文本
    public boolean searchInClob(Clob clob, String searchText)
            throws SQLException, IOException {
        String content = clobToString(clob);
        return content.contains(searchText);
    }

    // 工具方法：获取CLOB内容的单词数量
    public int getWordCount(Clob clob) throws SQLException, IOException {
        String content = clobToString(clob);
        if (content == null || content.trim().isEmpty()) {
            return 0;
        }
        return content.trim().split("\\s+").length;
    }

    // 清理所有CLOB资源
    public void cleanupClobs(Clob... clobs) {
        for (Clob clob : clobs) {
            if (clob != null) {
                try {
                    clob.free();
                } catch (SQLException e) {
                     // 忽略资源清理过程中的异常
                }
            }
        }
    }
}

// 演示用数据实体类
class Article {
    private int id;
    private String title;
    private Clob content;
    private String author;
    private Date createdAt;
    // Getter与Setter方法
}
```

