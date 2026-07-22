JDBC provides support for Large Objects (LOB) and XML data types, including BLOB (Binary Large Object), CLOB (Character Large Object), and XML type operations.

## BLOB Data Processing

BLOB (Binary Large Object) is used to store large binary data, such as images, audio, video, etc.

### Insert BLOB Data

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

        // Load driver
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

            // Create table if not exists
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

            // Prepare insert statement
            String sql = "INSERT INTO DOCUMENTS (DOC_ID, DOC_NAME, DOC_TYPE, CONTENT) VALUES (?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            // Set basic parameters
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "sample_image.jpg");
            pstmt.setString(3, "IMAGE");

            // Simulate BLOB data (in real use, read from file)
            byte[] fileContent = "This is sample image content".getBytes();

            // Create BLOB object
            Blob blob = conn.createBlob();
            OutputStream outputStream = blob.setBinaryStream(1);
            outputStream.write(fileContent);
            outputStream.close();

            // Set BLOB parameter
            pstmt.setBlob(4, blob);

            // Execute insert
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

            // Free LOB resources
            blob.free();

            // Commit transaction
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

### Query BLOB Data

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

        // Load driver
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

                // Get BLOB object
                Blob blob = rs.getBlob("CONTENT");

                if (blob != null) {
                    long blobLength = blob.length();
                    System.out.println("Document ID: " + docId);
                    System.out.println("Document Name: " + docName);
                    System.out.println("BLOB Length: " + blobLength + " bytes");

                    // Read BLOB data
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

### Update BLOB Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.nio.file.*;

public class BLOBUpdateExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        Blob blob = null;
        OutputStream outputStream = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Prepare update statement
            String sql = "UPDATE DOCUMENTS SET CONTENT = ? WHERE DOC_ID = ?";
            pstmt = conn.prepareStatement(sql);

            // Set update condition
            pstmt.setInt(2, 1001);

            // Simulate updated file content
            byte[] fileContent = "Updated image content".getBytes();

            // Create BLOB object
            blob = conn.createBlob();
            outputStream = blob.setBinaryStream(1);
            outputStream.write(fileContent);
            outputStream.close();
            outputStream = null;

            // Set BLOB parameter - use setBytes method
            pstmt.setBytes(1, fileContent);

            // Execute update
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " document(s)");
            System.out.println("New file size: " + fileContent.length + " bytes");

            // Free LOB resources
            blob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // Close resources
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

## CLOB Data Processing
CLOB (Character Large Object) is used to store large text data, such as articles, logs, configuration files, etc.

### Insert CLOB Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBInsertExample {
    public static void main(String[] args) {
        // Database connection parameters
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
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Create table if not exists
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

            // Prepare insert statement
            String sql = "INSERT INTO ARTICLES (ARTICLE_ID, TITLE, CONTENT, AUTHOR) VALUES (?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            // Set basic parameters
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "JDBC programming guide"); 
            pstmt.setString(4, "zhangsan"); 

            // Simulate large text content
            String fileContent = "This is a sample article";

            // Create CLOB object
            clob = conn.createClob();
            writer = clob.setCharacterStream(1);
            writer.write(fileContent);
            writer.close();
            writer = null;

            // Set CLOB parameter - use setString method
            pstmt.setString(3, fileContent);

            // Execute insert
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

            // Free LOB resources
            clob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // Close resources
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

### Query CLOB Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBQueryExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        Reader reader = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Execute query
            stmt = conn.createStatement();
            rs = stmt.executeQuery("SELECT ARTICLE_ID, TITLE, CONTENT FROM ARTICLES WHERE ARTICLE_ID = 1001");

            if (rs.next()) {
                int articleId = rs.getInt("ARTICLE_ID");
                String title = rs.getString("TITLE");

                // Get CLOB object
                Clob clob = rs.getClob("CONTENT");

                if (clob != null) {
                    long clobLength = clob.length();
                    System.out.println("Article ID: " + articleId);
                    System.out.println("Title: " + title);
                    System.out.println("CLOB Length: " + clobLength + " characters");

                    // Read CLOB data
                    reader = clob.getCharacterStream();
                    StringBuilder contentBuilder = new StringBuilder();

                    char[] buffer = new char[1024];
                    int charsRead;

                    while ((charsRead = reader.read(buffer)) != -1) {
                        contentBuilder.append(buffer, 0, charsRead);
                    }

                    String articleContent = contentBuilder.toString();
                    System.out.println("Read " + articleContent.length() + " characters from CLOB");

                    // Print partial content
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
            // Close resources
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

### Update CLOB Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class CLOBUpdateExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        Clob clob = null;
        Writer writer = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Prepare update statement
            String sql = "UPDATE ARTICLES SET CONTENT = ? WHERE ARTICLE_ID = ?";
            pstmt = conn.prepareStatement(sql);

            // Set update condition
            pstmt.setInt(2, 1001);

            // Prepare new content
            String newContent = "This is a new content for article";

            // Create CLOB object
            clob = conn.createClob();
            writer = clob.setCharacterStream(1);
            writer.write(newContent);
            writer.close();
            writer = null;

            // Set CLOB parameter - use setString method
            pstmt.setString(1, newContent);

            // Execute update
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " article(s)");
            System.out.println("New content length: " + newContent.length() + " characters");

            // Free LOB resources
            clob.free();

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // Close resources
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

## XML Data Processing
The SQLXML interface provides functionality to process XML data, enabling creation, reading, and operation of XMLTYPE type data.

### Create and Insert XML Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLInsertExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        PreparedStatement pstmt = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Create table if not exists
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

            // Prepare insert statement
            String sql = "INSERT INTO XML_DATA (ID, NAME, XML_CONTENT) VALUES (?, ?, XMLPARSE(DOCUMENT ?))";
            pstmt = conn.prepareStatement(sql);

            // Set basic parameters
            pstmt.setInt(1, 1001);
            pstmt.setString(2, "product comment"); 

            // Set XML content
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

            // Execute insert
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
            // Close resources
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

### Query XML Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLQueryExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Execute query
            stmt = conn.createStatement();
            rs = stmt.executeQuery("SELECT ID, NAME, XML_CONTENT FROM XML_DATA WHERE ID = 1001");

            if (rs.next()) {
                int id = rs.getInt("ID");
                String name = rs.getString("NAME");

                // Use getObject to read XML type
                Object xmlObj = rs.getObject("XML_CONTENT");
                String xmlContent = xmlObj != null ? xmlObj.toString() : "";

                System.out.println("ID: " + id);
                System.out.println("Name: " + name);
                System.out.println("XML Content: " + xmlContent);

                // Simple parse product name
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
            // Close resources
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

### Update XML Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLUpdateExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Prepare update statement
            String sql = "UPDATE XML_DATA SET XML_CONTENT = XMLPARSE(DOCUMENT ?) WHERE ID = ?";

            pstmt = conn.prepareStatement(sql);

            // Set update condition
            pstmt.setInt(2, 1001);

            // Prepare updated XML content
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

            // Execute update
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " XML record(s)");

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources
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

## Stream Processing Large Objects

Stream processing can efficiently handle large objects, reducing memory usage.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.nio.file.*;

public class StreamLOBExample {
    public static void main(String[] args) {
        // Database connection parameters
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
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Create table if not exists
            Statement createStmt = conn.createStatement();
            try {
                createStmt.execute("CREATE TABLE IF NOT EXISTS video_files(file_id INTEGER PRIMARY KEY, file_name VARCHAR(100), content BLOB)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // Delete old data if exists
            try {
                createStmt.execute("DELETE FROM video_files WHERE file_id = 3001");
            } catch (SQLException e) {
                System.out.println("Delete info: " + e.getMessage());
            }
            createStmt.close();

            // Stream insert BLOB
            String insertSql = "INSERT INTO video_files (file_id, file_name, content) " +
                    "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(insertSql);

            pstmt.setInt(1, 3001);
            pstmt.setString(2, "sample_video.mp4");

            // Use setBinaryStream for stream insert
            String videoPath = "C:/temp/large_video.mp4";

            // Create sample file (if not exists)
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

            // Directly set file input stream as BLOB parameter
            // Use file size as length
            File file = new File(videoPath);
            long fileSize = file.length();
            pstmt.setBinaryStream(3, inputStream, fileSize);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted video file using stream");

            inputStream.close();
            inputStream = null;

            pstmt.close();
            pstmt = null;

            // Stream query BLOB
            String querySql = "SELECT file_id, file_name, content FROM video_files WHERE file_id = 3001";

            stmt = conn.createStatement();
            rs = stmt.executeQuery(querySql);

            if (rs.next()) {
                String fileName = rs.getString("FILE_NAME");
                Blob blob = rs.getBlob("CONTENT");

                if (blob != null) {
                    // Stream read BLOB
                    String outputPath = "C:/temp/output_" + fileName;

                    blobStream = blob.getBinaryStream();
                    fileOutput = new FileOutputStream(outputPath);

                    // 8KB buffer
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
            // Close resources
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

