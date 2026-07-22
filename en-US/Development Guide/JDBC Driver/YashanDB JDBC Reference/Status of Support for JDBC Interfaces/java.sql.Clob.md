## Description

The Clob interface represents that it is used to manipulate CLOB type data.

`java.sql.Clob` represents that it is the core interface in the JDBC specification specifically designed for operating large character objects in databases. In simple terms, it serves as the interaction bridge between Java programs and `CLOB` type fields in the database.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Clob.html).

## Method

The YashanDB JDBC driver has the following methods for the CLOB interface (Standalone Deployment / YAC / Distributed Cluster Deployment):

|Method |Return Type |
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

The YashanDB JDBC driver has the following methods for the CLOB interface (ISC Distributed Cluster Deployment):

|Method |Return Type |Notes |
|------------------------------------------------------|----------------------|------------------------------------------------|
| length()                                             | long                 |  -                                              |
| getSubString(long pos, int length)                   | String               | -                                               |
| setString(long pos, String str)                      | int                  | -                                               |
| setString(long pos, String str, int offset, int len) | int                  | In ISC Distributed Cluster Deployment, this method can only be used to write data to the temporary LOB on CN nodes, not to write data on DN nodes. |
| getCharacterStream()                                 | java.io.Reader       | -                                               |
| getAsciiStream()                                     | java.io.InputStream  | -                                               |
| setAsciiStream(long pos)                             | java.io.OutputStream | -                                               |
| setCharacterStream(long pos)                         | java.io.Writer       | -                                               |
| getCharacterStream(long pos, long length)            | Reader               | -                                               |
| truncate(long len)                                | void                 | In ISC Distributed Cluster Deployment, this method can only be used to write data to the temporary LOB on CN nodes, not to write data on DN nodes. |
| free()                                               | void                 | -                                               |

## Example

```java
package quickstart;

import java.io.*;
import java.sql.*;
import java.util.*;

public class ClobCompleteExample {

    // Create database connection
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

            // 1. Create and initialize a CLOB
            Clob clob = example.createAndInitializeClob(conn);

            // 2. Demonstrate all Clob methods
            example.demonstrateAllClobMethods(conn, clob);

            // 3. Database operations
            example.databaseClobOperations(conn);

            // 4. Real-world scenarios
            example.realWorldScenarios(conn);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Create and initialize a CLOB with sample data
    public Clob createAndInitializeClob(Connection conn) throws SQLException {
        System.out.println("1. Creating and initializing CLOB...");

        // Create empty CLOB
        Clob clob = conn.createClob();

        // Method 1: setString - set complete content
        String initialContent = "This is the initial CLOB content.\n" +
                               "CLOB stands for Character Large Object.\n" +
                               "It's used to store large text data in databases.";

        clob.setString(1, initialContent);
        System.out.println("   Initial CLOB created with " + clob.length() + " characters");

        return clob;
    }
    
    // Demonstrate all Clob interface methods
    public void demonstrateAllClobMethods(Connection conn, Clob clob)
            throws SQLException, IOException {
        System.out.println("2. Demonstrating all Clob methods:");

        // length() - Get CLOB length
        long length = clob.length();
        System.out.println("    length(): " + length + " characters");

        // getSubString() - Read part of CLOB
        String substring = clob.getSubString(1, 20);
        System.out.println("    getSubString(1, 20): \"" + substring + "\"");

        // setString() with offset - Modify CLOB
        String newText = "Updated text using setString with offset.";
        clob.setString(30, newText, 0, 10);
        System.out.println("    setString() with offset completed");

        // getCharacterStream() - Read as stream
        try (Reader reader = clob.getCharacterStream()) {
            char[] buffer = new char[100];
            int charsRead = reader.read(buffer);
            System.out.println("    getCharacterStream() read " + charsRead + " chars");
        }

        // getCharacterStream() with range - Partial stream
        try (Reader reader = clob.getCharacterStream(10, 15)) {
            char[] buffer = new char[15];
            int charsRead = reader.read(buffer);
            System.out.println("    getCharacterStream(10, 15) read " + charsRead + " chars");
        }

        // getAsciiStream() - Read as ASCII stream
        try (InputStream is = clob.getAsciiStream()) {
            byte[] buffer = new byte[100];
            int bytesRead = is.read(buffer);
            System.out.println("    getAsciiStream() read " + bytesRead + " bytes");
        }

        // setCharacterStream() - Write via stream
        Clob newClob = conn.createClob();
        try (Writer writer = newClob.setCharacterStream(1)) {
            writer.write("Content written via setCharacterStream.");
        }
        System.out.println("    setCharacterStream() wrote to new CLOB");

        // setAsciiStream() - Write via ASCII stream
        Clob asciiClob = conn.createClob();
        try (OutputStream os = asciiClob.setAsciiStream(1)) {
            os.write("ASCII content".getBytes("US-ASCII"));
        }
        System.out.println("    setAsciiStream() wrote ASCII content");

        // truncate() - Trim CLOB
        clob.truncate(50);
        System.out.println("    truncate(50): CLOB now " + clob.length() + " chars");

        // free() - Release resources
        asciiClob.free();
        newClob.free();
        System.out.println("    free() released temporary CLOB resources");

        System.out.println();
    }
    

    // Database operations with CLOB
    public void databaseClobOperations(Connection conn) throws SQLException, IOException {
        System.out.println("3. Database CLOB Operations:");


        // Create table for demonstration
        createClobTable(conn);

        // Store CLOB in database
        Clob articleClob = conn.createClob();
        articleClob.setString(1, "This is a long article about JDBC and CLOB interfaces...");

        String insertSQL = "INSERT INTO clob_demo (id, title, content, author) VALUES (clob_demo_seq.NEXTVAL, ?, ?, ?)";
        try (PreparedStatement ps = conn.prepareStatement(insertSQL)) {
            ps.setString(1, "JDBC CLOB Tutorial");
            ps.setClob(2, articleClob);
            ps.setString(3, "Admin");
            ps.executeUpdate();

            System.out.println("    CLOB stored in database");

            // Clean up
            articleClob.free();
        }

        // Retrieve and process CLOB
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

                    // Read first 50 characters
                    String preview = storedClob.getSubString(1, 50);
                    System.out.println("      - Preview: " + preview + "...");
                    // Clean up
                    storedClob.free();
                }
            }
        }

        // Update CLOB in database
        Clob updatedClob = conn.createClob();
        updatedClob.setString(1, "Updated article content with more details...");

        String updateSQL = "UPDATE clob_demo SET content = ? WHERE title = ?";
        try (PreparedStatement ps = conn.prepareStatement(updateSQL)) {
            ps.setClob(1, updatedClob);
            ps.setString(2, "JDBC CLOB Tutorial");
            int rowsUpdated = ps.executeUpdate();

            System.out.println("    Updated " + rowsUpdated + " row(s) with new CLOB");
            // Clean up
            updatedClob.free();
        }

        System.out.println();
    }
    
    // Real-world CLOB usage scenarios
    public void realWorldScenarios(Connection conn) throws Exception {
        System.out.println("4. Real-world CLOB Scenarios:");

        // Scenario 1: Large text document storage
        System.out.println("   Scenario 1: Large Document Storage");

        Clob document = conn.createClob();
        StringBuilder largeContent = new StringBuilder();
        for (int i = 1; i <= 1000; i++) {
            largeContent.append("Line ").append(i).append(": Sample text for CLOB storage.\n");
        }

        document.setString(1, largeContent.toString());
        System.out.println("      - Created document with " + document.length() + " characters");
        document.free();

        // Scenario 2: Log file storage
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

        // Scenario 3: XML/JSON data storage
        System.out.println("   Scenario 3: XML/JSON Data Storage");

        String jsonData = "{\"user\": {\"id\": 1, \"name\": \"John\", \"preferences\": " +
                         "{\"theme\": \"dark\", \"language\": \"en\"}}}";

        Clob jsonClob = conn.createClob();
        jsonClob.setString(1, jsonData);

        // Extract specific part
        String userName = jsonClob.getSubString(
            jsonData.indexOf("\"name\": \"") + 9,
            "John".length()
        );
        System.out.println("      - Stored JSON, extracted name: " + userName);
        jsonClob.free();

        // Scenario 4: Email/content management
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

        // Count lines in email
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
    
    // Create demonstration table
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
                // Table might not exist, ignore
            }
            stmt.execute(createSQL);
            try {
                stmt.execute("CREATE SEQUENCE clob_demo_seq START WITH 1 INCREMENT BY 1");
            } catch (SQLException e) {
                // Sequence might exist, ignore
            }
        }
    }

    // Utility method: Convert CLOB to String safely
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

    // Utility method: Create CLOB from file
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

    // Utility method: Append text to existing CLOB
    public void appendToClob(Clob clob, String text) throws SQLException {
        long currentLength = clob.length();
        clob.setString(currentLength + 1, text);
    }

    // Utility method: Search text in CLOB
    public boolean searchInClob(Clob clob, String searchText)
            throws SQLException, IOException {
        String content = clobToString(clob);
        return content.contains(searchText);
    }

    // Utility method: Get word count in CLOB
    public int getWordCount(Clob clob) throws SQLException, IOException {
        String content = clobToString(clob);
        if (content == null || content.trim().isEmpty()) {
            return 0;
        }
        return content.trim().split("\\s+").length;
    }

    // Clean up all CLOB resources
    public void cleanupClobs(Clob... clobs) {
        for (Clob clob : clobs) {
            if (clob != null) {
                try {
                    clob.free();
                } catch (SQLException e) {
                    // Ignore cleanup errors
                }
            }
        }
    }
}

// Data class for demonstration
class Article {
    private int id;
    private String title;
    private Clob content;
    private String author;
    private Date createdAt;
    // Getters and setters
}
```

