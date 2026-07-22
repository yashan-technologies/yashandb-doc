SQLXML is an interface introduced in JDBC 4.0 for handling XML data in Java applications.

XML data in YashanDB is stored as XMLTYPE type. YashanDB JDBC implements a series of methods for reading and operating on YashanDB's XMLTYPE type data.

## Interface Methods

1. Use the createSQLXML method of the Connection interface to create an empty SQLXML object.

2. Use the following interface methods to read and write SQLXML objects:

   |Class               |Method                                              |
   | -------------------------- | ------------------------------------------------------------ |
   | java.sql.ResultSet         | getSQLXML(int index)<br>getSQLXML(String columnName)         |
   | java.sql.ResultSet         | getObject(int index)<br>getObject(String columnName)         |
   | java.sql.PreparedStatement | setSQLXML(int index, SQLXML xmlObject)                       |

3. Use the following interface methods to read and write XML data in SQLXML objects:

   |Class    |Method                 |
   | --------------- | ------------------------------- |
   | java.sql.SQLXML | getString()                     |
   | java.sql.SQLXML | setString(String value)         |
   | java.sql.SQLXML | getCharacterStream()            |
   | java.sql.SQLXML | setCharacterStream()            |
   | java.sql.SQLXML | getBinaryStream()               |
   | java.sql.SQLXML | setBinaryStream()               |
   | java.sql.SQLXML | getSource(Class<T> sourceClass) |
   | java.sql.SQLXML | setResult(Class<T> resultClass) |

4. Use the free method of the SQLXML interface to release resources associated with the SQLXML object.

The usage rules for reading and writing SQLXML objects and reading and writing data within SQLXML objects are as follows:

- Newly created SQLXML objects can only be used to write XML data and cannot be read.

- SQLXML objects returned by get methods can only be used to read XML data and cannot be written to.

- A SQLXML object that is created or retrieved can only perform one write or read operation. Subsequent read/write operations are invalid.

## Creating SQLXML Object Practice

Use the createSQLXML method of the Connection interface to create an empty SQLXML object.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SQLXMLCreationExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        // Username
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
        try {
            conn = DriverManager.getConnection(url, info);

            // Create SQLXML object
            SQLXML sqlXml = conn.createSQLXML();

            System.out.println("Created SQLXML object successfully");

            // Release resources
            sqlXml.free();

        } catch (SQLException e) {
            System.out.println("Error creating SQLXML: " + e.getMessage());
            e.printStackTrace();
        } finally {
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

### Reading and Writing SQLXML Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SQLXMLReadWriteExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        try {
            // Load YashanDB driver
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Create table if not exists
            String createTableSql = "CREATE TABLE IF NOT EXISTS XML_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "NAME VARCHAR(100)," +
                    "XML_CONTENT XMLTYPE" +
                    ")";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");

                // Delete old data to re-test
                stmt.execute("DELETE FROM XML_DATA1 WHERE ID = 1001");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // Insert XML data using XMLPARSE function
            String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                                 "<configuration>" +
                                 "  <product>" +
                                 "    <name>High performance server</name>" +
                                 "    <specs>" +
                                 "      <cpu>16 cores</cpu>" +
                                 "      <memory>64GB</memory>" +
                                 "      <storage>1TB</storage>" +
                                 "      <network>10Gbps</network>" +
                                 "    </specs>" +
                                 "  </product>" +
                                 "</configuration>";

            String insertSql = "INSERT INTO XML_DATA1 (ID, NAME, XML_CONTENT) VALUES (1001, 'test', XMLPARSE(DOCUMENT ?))";

            try (PreparedStatement pstmt = conn.prepareStatement(insertSql)) {
                pstmt.setString(1, xmlContent);
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Inserted " + affectedRows + " rows");

                // Query and read XML data
                String querySql = "SELECT id, name, XML_CONTENT FROM XML_DATA1 WHERE id = 1001";

                try (Statement stmt = conn.createStatement();
                     ResultSet rs = stmt.executeQuery(querySql)) {

                    if (rs.next()) {
                        String id = rs.getString("ID");
                        String name = rs.getString("NAME");
                        // Use getObject to read XML type, then convert to string
                        Object xmlObj = rs.getObject("XML_CONTENT");
                        String content = xmlObj != null ? xmlObj.toString() : "";

                        System.out.println("Retrieved data:");
                        System.out.println("ID: " + id);
                        System.out.println("Name: " + name);
                        System.out.println("XML Length: " + content.length());
                        System.out.println("XML Content:");
                        System.out.println(content);
                    }
                } 

            } catch (SQLException e) {
                System.out.println("Error reading XML data: " + e.getMessage());
                e.printStackTrace();
            }

        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### Handling XML with PreparedStatement

```java
package quickstart;
import java.sql.*;
import java.util.Properties;

public class SQLXMLWithPreparedStatementExample {
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
        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false); 

            // Create table if not exists
            String createTableSql = "CREATE TABLE IF NOT EXISTS XML_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "NAME VARCHAR(100)," +
                    "XML_CONTENT XMLTYPE" +
                    ")";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // Batch insert XML data
            String sql = "INSERT INTO XML_DATA1 (id, name, xml_content) " +
                         "VALUES (?, ?, XMLPARSE(DOCUMENT ?))";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // Prepare multiple XML records
                String[] names = {"product A", "product B", "product C"};
                int[] quantities = {100, 200, 150};

                for (int i = 0; i < names.length; i++) {
                    // Set parameters
                    pstmt.setInt(1, 2000 + i);
                    pstmt.setString(2, names[i]);

                    // Create XML content
                    String xmlContent = String.format(
                        "<product>" +
                        "  <name>%s</name>" +
                        "  <quantity>%d</quantity>" +
                        "</product>",
                        names[i], quantities[i]);

                    // Use setString to set XML content
                    pstmt.setString(3, xmlContent);

                    // Add to batch
                    pstmt.addBatch();

                    System.out.println("Added " + names[i] + " to batch");
                }

                // Execute batch
                int[] updateCounts = pstmt.executeBatch();
                int totalAffected = java.util.Arrays.stream(updateCounts).sum();

                System.out.println("Batch insert completed, total affected: " + totalAffected);

                // Commit transaction
                conn.commit();

            } catch (SQLException e) {
                System.out.println("Error in batch processing: " + e.getMessage());
                e.printStackTrace();
            }
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
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

### Updating XML Data

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SQLXMLUpdateExample {
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
        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false); 

            // First create table and insert initial data
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS XML_DATA1(id INT PRIMARY KEY, name VARCHAR(100), xml_content XMLTYPE)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // Insert initial data (if not exists)
            try {
                stmt.execute("DELETE FROM XML_DATA1 WHERE id = 1001");
                stmt.execute("INSERT INTO XML_DATA1 (id, name, xml_content) VALUES (1001, 'test', XMLPARSE(DOCUMENT '<product><name>servers</name></product>'))");
            } catch (SQLException e) {
                System.out.println("Insert initial data info: " + e.getMessage());
            }
            stmt.close();

            // Prepare update statement - use XMLPARSE to convert string to XML
            String sql = "UPDATE XML_DATA1 SET xml_content = XMLPARSE(DOCUMENT ?) WHERE id = ?";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // Prepare updated XML content
                String updatedContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                                           "<product>" +
                                           "  <name>update servers</name>" +
                                           "  <specs>" +
                                           "    <cpu>32 cores</cpu>" +
                                           "    <memory>128GB</memory>" +
                                           "    <storage>2TB</storage>" +
                                           "    <network>20Gbps</network>" +
                                           "  </specs>" +
                                           "</product>";

                // Set parameters
                pstmt.setString(1, updatedContent);
                pstmt.setInt(2, 1001);

                // Execute update
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Updated " + affectedRows + " row(s)");

                // Commit transaction
                conn.commit();

            } catch (SQLException e) {
                System.out.println("Error updating XML data: " + e.getMessage());
                e.printStackTrace();
            } finally {
                if (conn != null) {
                    try {
                        conn.close();
                    } catch (SQLException e) {
                        e.printStackTrace();
                    }
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### XML Type Conversion

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class XMLConversionExample {
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
        try {
            conn = DriverManager.getConnection(url, info);

            // Query and update XML data
            String updateSql = "UPDATE XML_DATA1 SET xml_content = XMLPARSE(DOCUMENT ?) WHERE id = ?";

            try (PreparedStatement pstmt = conn.prepareStatement(updateSql)) {
                String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                        "<product>" +
                        "  <name>Conversion Test</name>" +
                        "</product>";

                pstmt.setString(1, xmlContent);
                pstmt.setInt(2, 1001);

                int affectedRows = pstmt.executeUpdate();
                System.out.println("Updated " + affectedRows + " row(s)");
                System.out.println("XML conversion example completed");

            } catch (SQLException e) {
                System.out.println("Error in XML conversion: " + e.getMessage());
                e.printStackTrace();
            }

        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
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

### Deleting XML Data

```java
package quickstart;
import java.sql.*;
import java.util.Properties;

public class SQLXMLDeletionExample {
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
        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false); 

            // Delete XML data
            String sql = "DELETE FROM XML_DATA1 WHERE id = ?";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // Delete record with ID 2000
                pstmt.setInt(1, 2000); 

                int affectedRows = pstmt.executeUpdate();

                System.out.println("Deleted " + affectedRows + " record(s)");

                // Commit transaction
                conn.commit();

            } catch (SQLException e) {
                System.out.println("Error in deletion: " + e.getMessage());
                e.printStackTrace();
            }
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
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

### Streaming XML Processing

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;

public class StreamingXMLExample {
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
        try {
            conn = DriverManager.getConnection(url, info);
            // Disable auto-commit
            conn.setAutoCommit(false); 

            // Create table if not exists
            String createTableSql = "CREATE TABLE IF NOT EXISTS XML_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "NAME VARCHAR(100)," +
                    "XML_CONTENT XMLTYPE" +
                    ")";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // Stream insert large XML document
            String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
                    "<configuration>" +
                    "  <product>" +
                    "    <name>High performance server</name>" +
                    "    <specs>" +
                    "      <cpu>16 cores</cpu>" +
                    "      <memory>64GB</memory>" +
                    "      <storage>1TB</storage>" +
                    "      <network>10Gbps</network>" +
                    "    </specs>" +
                    "  </product>" +
                    "</configuration>";

            // Insert into database
            String sql = "INSERT INTO XML_DATA1 (id, name, xml_content) VALUES (3000, 'stream_test', XMLPARSE(DOCUMENT ?))";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
                pstmt.setString(1, xmlContent);

                int affectedRows = pstmt.executeUpdate();
                System.out.println("Streamed insert completed, affected rows: " + affectedRows);

                // Commit transaction
                conn.commit();
            }

        } catch (SQLException e) {
            System.out.println("Error streaming XML: " + e.getMessage());
            e.printStackTrace();
        } finally {
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

## SQLXML Data Sources

### Creating SQLXML from File

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.net.*;

public class SQLXMLFromFileExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        FileReader reader = null;
        SQLXML sqlXml = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Create table if not exists
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS config_data(id INTEGER, name VARCHAR(100), xml_content CLOB)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }
            stmt.close();

            // Read XML file content
            String filePath = "C:/data/config.xml";
            StringBuilder xmlContent = new StringBuilder();

            // Create sample XML file (if not exists)
            try {
                java.io.File file = new java.io.File(filePath);
                if (!file.exists()) {
                    file.getParentFile().mkdirs();
                    java.io.FileWriter fw = new java.io.FileWriter(file);
                    fw.write("<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<config><setting>value</setting></config>");
                    fw.close();
                }
            } catch (Exception e) {
                System.out.println("File creation info: " + e.getMessage());
            }

            reader = new FileReader(filePath);
            int ch;
            while ((ch = reader.read()) != -1) {
                xmlContent.append((char) ch);
            }
            reader.close();
            reader = null;

            // Create SQLXML object
            sqlXml = conn.createSQLXML();
            sqlXml.setString(xmlContent.toString());

            // Insert into database
            String sql = "INSERT INTO config_data (id, name, xml_content) " +
                         "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(sql);

            pstmt.setInt(1, 2000); 
            // Name
            pstmt.setString(2, "system configuration"); 
            pstmt.setSQLXML(3, sqlXml);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted config data, affected rows: " + affectedRows);

            // Release resources
            if (sqlXml != null) {
                sqlXml.free();
            }

        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());
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

### Creating SQLXML from URL

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.net.*;

public class SQLXMLFromURLExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        InputStream is = null;
        SQLXML sqlXml = null;

        try {
            // Load YashanDB JDBC driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // Read XML from URL
            String xmlUrl = "https://example.com/config.xml";

            // Create sample XML content (since network URL may not be accessible)
            String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<config><setting>sample</setting></config>";

            // Create SQLXML object
            sqlXml = conn.createSQLXML();
            sqlXml.setString(xmlContent);

            System.out.println("Created SQLXML from sample content");
            // Directly use the already set xmlContent

            // Insert into database
            String sql = "INSERT INTO config_data (id, name, xml_content) " +
                         "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(sql);

            pstmt.setInt(1, 3000); 
            // Remote config name
            pstmt.setString(2, "remote_config"); 
            pstmt.setSQLXML(3, sqlXml);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted remote config, affected rows: " + affectedRows);

            // Release resources
            if (sqlXml != null) {
                sqlXml.free();
            }

        
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();

        // Catch and handle SQLException
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // Close resources
            try {
                if (is != null) {
                    is.close();
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

