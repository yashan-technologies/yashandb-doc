JDBC提供了对大对象（LOB）和XML数据类型的支持，包括BLOB（二进制大对象）、CLOB（字符大对象）和XML类型等操作。

## BLOB数据处理

BLOB（Binary Large Object）用于存储大量的二进制数据，如图片、音频、视频等。

### 插入BLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class BLOBInsertExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        PreparedStatement pstmt = null;
        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false); 

            // 创建表（如果不存在）
            String createTableSql = "CREATE TABLE IF NOT EXISTS DOCUMENTS (" +
                    "DOC_ID INT PRIMARY KEY," +
                    "DOC_NAME VARCHAR(100)," +
                    "DOC_TYPE VARCHAR(50)," +
                    "CONTENT BLOB" +
                    ")";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // 准备插入语句
            String sql = "INSERT INTO DOCUMENTS (DOC_ID, DOC_NAME, DOC_TYPE, CONTENT) VALUES (?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            // 设置基本参数
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "sample_image.jpg");
            pstmt.setString(3, "IMAGE");

            // 模拟BLOB数据（实际使用时读取文件）
            byte[] fileContent = "This is sample image content".getBytes();

            // 创建BLOB对象
            Blob blob = conn.createBlob();
            OutputStream outputStream = blob.setBinaryStream(1);
            outputStream.write(fileContent);
            outputStream.close();

            // 设置BLOB参数
            pstmt.setBlob(4, blob);

            // 执行插入
            try {
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Inserted " + affectedRows + " document(s)");
                System.out.println("File size: " + fileContent.length + " bytes");
            } catch (SQLException e) {
                if (e.getMessage().contains("unique constraint") || e.getMessage().contains("YAS-02030")) {
                    System.out.println("Document already exists, skipping insert");
                } else {
                    throw e;
                }
            }

            // 释放LOB资源
            blob.free();

            // 提交事务
            conn.commit();

        } catch (SQLException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (pstmt != null) {
                try {
                    pstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### 查询BLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class BLOBQueryExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        try {
            conn = DriverManager.getConnection(url, info);
            stmt = conn.createStatement();
            rs = stmt.executeQuery("SELECT DOC_ID, DOC_NAME, CONTENT FROM DOCUMENTS WHERE DOC_ID = 1001");

            if (rs.next()) {
                int docId = rs.getInt("DOC_ID");
                String docName = rs.getString("DOC_NAME");

                // 获取BLOB对象
                Blob blob = rs.getBlob("CONTENT");

                if (blob != null) {
                    long blobLength = blob.length();
                    System.out.println("Document ID: " + docId);
                    System.out.println("Document Name: " + docName);
                    System.out.println("BLOB Length: " + blobLength + " bytes");

                    // 读取BLOB数据
                    InputStream inputStream = blob.getBinaryStream();
                    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();

                    byte[] buffer = new byte[1024];
                    int bytesRead;

                    while ((bytesRead = inputStream.read(buffer)) != -1) {
                        outputStream.write(buffer, 0, bytesRead);
                    }

                    byte[] blobData = outputStream.toByteArray();
                    System.out.println("Read " + blobData.length + " bytes from BLOB");

                    inputStream.close();
                    outputStream.close();
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### 更新BLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.nio.file.*;

public class BLOBUpdateExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        Blob blob = null;
        OutputStream outputStream = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 准备更新语句
            String sql = "UPDATE DOCUMENTS SET CONTENT = ? WHERE DOC_ID = ?";
            pstmt = conn.prepareStatement(sql);

            // 设置更新条件
            pstmt.setInt(2, 1001);

            // 模拟更新的文件内容
            byte[] fileContent = "Updated image content".getBytes();

            // 创建BLOB对象
            blob = conn.createBlob();
            outputStream = blob.setBinaryStream(1);
            outputStream.write(fileContent);
            outputStream.close();
            outputStream = null;

            // 设置BLOB参数 - 使用setBytes方法
            pstmt.setBytes(1, fileContent);

            // 执行更新
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " document(s)");
            System.out.println("New file size: " + fileContent.length + " bytes");

            // 释放LOB资源
            blob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (outputStream != null) {
                    outputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## CLOB数据处理
CLOB（Character Large Object）用于存储大量的文本数据，如文章、日志、配置文件等。

### 插入CLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBInsertExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        PreparedStatement pstmt = null;
        Clob clob = null;
        Writer writer = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 创建表（如果不存在）
            String createTableSql = "CREATE TABLE IF NOT EXISTS ARTICLES (" +
                    "ARTICLE_ID INT PRIMARY KEY," +
                    "TITLE VARCHAR(200)," +
                    "CONTENT CLOB," +
                    "AUTHOR VARCHAR(100)" +
                    ")";
            stmt = conn.createStatement();
            try {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }
            stmt.close();
            stmt = null;

            // 准备插入语句
            String sql = "INSERT INTO ARTICLES (ARTICLE_ID, TITLE, CONTENT, AUTHOR) VALUES (?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            // 设置基本参数
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "JDBC programming guide"); 
            pstmt.setString(4, "zhangsan"); 

            // 模拟大文本内容
            String fileContent = "This is a sample article";

            // 创建CLOB对象
            clob = conn.createClob();
            writer = clob.setCharacterStream(1);
            writer.write(fileContent);
            writer.close();
            writer = null;

            // 设置CLOB参数 - 使用setString方法
            pstmt.setString(3, fileContent);

            // 执行插入
            try {
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Inserted " + affectedRows + " article(s)");
                System.out.println("Article length: " + fileContent.length() + " characters");
            } catch (SQLException e) {
                if (e.getMessage().contains("unique constraint") || e.getMessage().contains("YAS-02030")) {
                    System.out.println("Article already exists, skipping insert");
                } else {
                    throw e;
                }
            }

            // 释放LOB资源
            clob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (writer != null) {
                    writer.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 查询CLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBQueryExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        Reader reader = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 执行查询
            stmt = conn.createStatement();
            rs = stmt.executeQuery("SELECT ARTICLE_ID, TITLE, CONTENT FROM ARTICLES WHERE ARTICLE_ID = 1001");

            if (rs.next()) {
                int articleId = rs.getInt("ARTICLE_ID");
                String title = rs.getString("TITLE");

                // 获取CLOB对象
                Clob clob = rs.getClob("CONTENT");

                if (clob != null) {
                    long clobLength = clob.length();
                    System.out.println("Article ID: " + articleId);
                    System.out.println("Title: " + title);
                    System.out.println("CLOB Length: " + clobLength + " characters");

                    // 读取CLOB数据
                    reader = clob.getCharacterStream();
                    StringBuilder contentBuilder = new StringBuilder();

                    char[] buffer = new char[1024];
                    int charsRead;

                    while ((charsRead = reader.read(buffer)) != -1) {
                        contentBuilder.append(buffer, 0, charsRead);
                    }

                    String articleContent = contentBuilder.toString();
                    System.out.println("Read " + articleContent.length() + " characters from CLOB");

                    // 打印部分内容
                    if (articleContent.length() > 100) {
                        System.out.println("First 100 characters: " + articleContent.substring(0, 100));
                    }

                    reader.close();
                    reader = null;
                }
            }

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (reader != null) {
                    reader.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (rs != null) {
                    rs.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 更新CLOB数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBUpdateExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        Clob clob = null;
        Writer writer = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 准备更新语句
            String sql = "UPDATE ARTICLES SET CONTENT = ? WHERE ARTICLE_ID = ?";
            pstmt = conn.prepareStatement(sql);

            // 设置更新条件
            pstmt.setInt(2, 1001);

            // 准备新的内容
            String newContent = "This is a new content for article";

            // 创建CLOB对象
            clob = conn.createClob();
            writer = clob.setCharacterStream(1);
            writer.write(newContent);
            writer.close();
            writer = null;

            // 设置CLOB参数 - 使用setString方法
            pstmt.setString(1, newContent);

            // 执行更新
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " article(s)");
            System.out.println("New content length: " + newContent.length() + " characters");

            // 释放LOB资源
            clob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (writer != null) {
                    writer.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## XML数据处理
SQLXML接口提供了处理XML数据的功能，可以创建、读取和操作XMLTYPE类型的数据。

### 创建和插入XML数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLInsertExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        PreparedStatement pstmt = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 创建表（如果不存在）
            String createTableSql = "CREATE TABLE IF NOT EXISTS XML_DATA (" +
                    "ID INT PRIMARY KEY," +
                    "NAME VARCHAR(100)," +
                    "XML_CONTENT XMLTYPE" +
                    ")";
            stmt = conn.createStatement();
            try {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }
            stmt.close();
            stmt = null;

            // 准备插入语句
            String sql = "INSERT INTO XML_DATA (ID, NAME, XML_CONTENT) VALUES (?, ?, XMLPARSE(DOCUMENT ?))";
            pstmt = conn.prepareStatement(sql);

            // 设置基本参数
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "product comment"); 

            // 设置XML内容
            String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                             "<configuration>" +
                             "  <product>" +
                             "    <name>High performance server</name>" +
                             "    <specs>" +
                             "      <cpu>16 cores</cpu>" +
                             "      <memory>64GB</memory>" +
                             "      <storage>1TB</storage>" +
                             "    </specs>" +
                             "  </product>" +
                             "</configuration>";

            pstmt.setString(3, xmlContent);

            // 执行插入
            try {
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Inserted " + affectedRows + " XML record(s)");
            } catch (SQLException e) {
                if (e.getMessage().contains("unique constraint") || e.getMessage().contains("YAS-02030")) {
                    System.out.println("XML record already exists, skipping insert");
                } else {
                    throw e;
                }
            }

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 查询XML数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLQueryExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 执行查询
            stmt = conn.createStatement();
            rs = stmt.executeQuery("SELECT ID, NAME, XML_CONTENT FROM XML_DATA WHERE ID = 1001");

            if (rs.next()) {
                int id = rs.getInt("ID");
                String name = rs.getString("NAME");

                // 使用getObject读取XML类型
                Object xmlObj = rs.getObject("XML_CONTENT");
                String xmlContent = xmlObj != null ? xmlObj.toString() : "";

                System.out.println("ID: " + id);
                System.out.println("Name: " + name);
                System.out.println("XML Content: " + xmlContent);

                // 简单解析产品名称
                if (xmlContent.contains("<name>")) {
                    int startIndex = xmlContent.indexOf("<name>") + 5;
                    int endIndex = xmlContent.indexOf("</name>");
                    if (startIndex > 0 && endIndex > startIndex) {
                        String productName = xmlContent.substring(startIndex, endIndex);
                        System.out.println("Product Name: " + productName);
                    }
                }
            }

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (rs != null) {
                    rs.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 更新XML数据

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLUpdateExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 准备更新语句
            String sql = "UPDATE XML_DATA SET XML_CONTENT = XMLPARSE(DOCUMENT ?) WHERE ID = ?";

            pstmt = conn.prepareStatement(sql);

            // 设置更新条件
            pstmt.setInt(2, 1001);

            // 准备更新的XML内容
            String updatedContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                                  "<configuration>" +
                                  "  <product>" +
                                  "    <name>Enterprise server</name>" +
                                  "    <specs>" +
                                  "      <cpu>32 cores</cpu>" +
                                  "      <memory>128GB</memory>" +
                                  "      <storage>2TB</storage>" +
                                  "      <network>10Gbps</network>" +
                                  "    </specs>" +
                                  "  </product>" +
                                  "</configuration>";

            pstmt.setString(1, updatedContent);

            // 执行更新
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " XML record(s)");

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 流式处理大对象

使用流式处理可以高效地处理大对象，减少内存使用。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.nio.file.*;

public class StreamLOBExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        Statement stmt = null;
        ResultSet rs = null;
        InputStream inputStream = null;
        InputStream blobStream = null;
        FileOutputStream fileOutput = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 创建表（如果不存在）
            Statement createStmt = conn.createStatement();
            try {
                createStmt.execute("CREATE TABLE IF NOT EXISTS video_files(file_id INTEGER PRIMARY KEY, file_name VARCHAR(100), content BLOB)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // 删除旧数据（如果存在）
            try {
                createStmt.execute("DELETE FROM video_files WHERE file_id = 3001");
            } catch (SQLException e) {
                System.out.println("Delete info: " + e.getMessage());
            }
            createStmt.close();

            // 使用流式插入BLOB
            String insertSql = "INSERT INTO video_files (file_id, file_name, content) " +
                    "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(insertSql);

            pstmt.setInt(1, 3001);
            pstmt.setString(2, "sample_video.mp4");

            // 使用setBinaryStream流式插入
            String videoPath = "C:/temp/large_video.mp4";

            // 创建示例文件（如果不存在）
            try {
                java.io.File file = new java.io.File("C:/temp");
                if (!file.exists()) {
                    file.mkdirs();
                }
                file = new java.io.File(videoPath);
                if (!file.exists()) {
                    java.io.FileWriter fw = new java.io.FileWriter(file);
                    fw.write("Sample video content for testing");
                    fw.close();
                }
            } catch (Exception e) {
                System.out.println("File creation info: " + e.getMessage());
            }

            inputStream = new FileInputStream(videoPath);

            // 直接将文件输入流设置为BLOB参数
            // 使用文件大小作为长度
            File file = new File(videoPath);
            long fileSize = file.length();
            pstmt.setBinaryStream(3, inputStream, fileSize);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted video file using stream");

            inputStream.close();
            inputStream = null;

            pstmt.close();
            pstmt = null;

            // 使用流式查询BLOB
            String querySql = "SELECT file_id, file_name, content FROM video_files WHERE file_id = 3001";

            stmt = conn.createStatement();
            rs = stmt.executeQuery(querySql);

            if (rs.next()) {
                String fileName = rs.getString("FILE_NAME");
                Blob blob = rs.getBlob("CONTENT");

                if (blob != null) {
                    // 使用流式读取BLOB
                    String outputPath = "C:/temp/output_" + fileName;

                    blobStream = blob.getBinaryStream();
                    fileOutput = new FileOutputStream(outputPath);

                    // 8KB缓冲区
                    byte[] buffer = new byte[8192];
                    int bytesRead;
                    long totalBytes = 0;

                    while ((bytesRead = blobStream.read(buffer)) != -1) {
                        fileOutput.write(buffer, 0, bytesRead);
                        totalBytes += bytesRead;
                    }

                    System.out.println("Downloaded " + totalBytes + " bytes to " + outputPath);

                    blobStream.close();
                    blobStream = null;
                    fileOutput.close();
                    fileOutput = null;
                }
            }

            rs.close();
            rs = null;
            stmt.close();
            stmt = null;

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
            e.printStackTrace();
        } catch (IOException e) {
            System.out.println("Error reading/writing file: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (fileOutput != null) {
                    fileOutput.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (blobStream != null) {
                    blobStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (inputStream != null) {
                    inputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (rs != null) {
                    rs.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

