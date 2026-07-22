SQLXML是JDBC 4.0引入的接口，用于在Java应用程序中处理XML数据。

YashanDB中的XML数据以XMLTYPE类型进行存储，YashanDB JDBC实现了一系列方法用于读取和操作YashanDB的XMLTYPE类型数据。

## 接口方法

1. 使用Connection接口的createSQLXML方法类来创建一个空的SQLXML对象。

2. 使用如下接口方法读写SQLXML对象：

   | 类| 方法|
   | -------------------------- | ------------------------------------------------------------ |
   | java.sql.ResultSet         | getSQLXML(int index)<br>getSQLXML(String columnName)         |
   | java.sql.ResultSet         | getObject(int index)<br>getObject(String columnName)         |
   | java.sql.PreparedStatement | setSQLXML(int index, SQLXML xmlObject)                       |

3. 使用如下接口方法读写SQLXML对象中的XML数据：

   | 类| 方法|
   | --------------- | ------------------------------- |
   | java.sql.SQLXML | getString()                     |
   | java.sql.SQLXML | setString(String value)         |
   | java.sql.SQLXML | getCharacterStream()            |
   | java.sql.SQLXML | setCharacterStream()            |
   | java.sql.SQLXML | getBinaryStream()               |
   | java.sql.SQLXML | setBinaryStream()               |
   | java.sql.SQLXML | getSource(Class<T> sourceClass) |
   | java.sql.SQLXML | setResult(Class<T> resultClass) |

4. 使用SQLXML接口的free方法释放SQLXML对象关联的资源。

上述读写SQLXML对象以及读写SQLXML对象中的数据的使用规则为：

- 新创建的SQLXML对象只能用于写入XML数据，不可读取。

- 通过get方法返回的SQLXML对象只能用于读取XML数据，不可写入。

- 一次创建或读取的SQLXML对象只可进行一次的写或读操作，之后的读写操作均无效。

## 创建SQLXML对象实践

使用Connection接口的createSQLXML方法类来创建一个空的SQLXML对象。

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

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, info);

            // 创建SQLXML对象
            SQLXML sqlXml = conn.createSQLXML();

            System.out.println("Created SQLXML object successfully");

            // 释放资源
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

### 读写SQLXML数据

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
            // 加载YashanDB驱动
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 先创建表（如果不存在）
            String createTableSql = "CREATE TABLE IF NOT EXISTS XML_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "NAME VARCHAR(100)," +
                    "XML_CONTENT XMLTYPE" +
                    ")";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");

                // 删除旧数据以便重新测试
                stmt.execute("DELETE FROM XML_DATA1 WHERE ID = 1001");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // 使用XMLPARSE函数插入XML数据
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

                // 查询并读取XML数据
                String querySql = "SELECT id, name, XML_CONTENT FROM XML_DATA1 WHERE id = 1001";

                try (Statement stmt = conn.createStatement();
                     ResultSet rs = stmt.executeQuery(querySql)) {

                    if (rs.next()) {
                        String id = rs.getString("ID");
                        String name = rs.getString("NAME");
                        // 使用getObject读取XML类型，然后转换为字符串
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

### 使用PreparedStatement处理XML

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

        // 先加载驱动
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

            // 先创建表（如果不存在）
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

            // 批量插入XML数据
            String sql = "INSERT INTO XML_DATA1 (id, name, xml_content) " +
                         "VALUES (?, ?, XMLPARSE(DOCUMENT ?))";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // 准备多条XML记录
                String[] names = {"product A", "product B", "product C"};
                int[] quantities = {100, 200, 150};

                for (int i = 0; i < names.length; i++) {
                    // 设置参数
                    pstmt.setInt(1, 2000 + i);
                    pstmt.setString(2, names[i]);

                    // 创建XML内容
                    String xmlContent = String.format(
                        "<product>" +
                        "  <name>%s</name>" +
                        "  <quantity>%d</quantity>" +
                        "</product>",
                        names[i], quantities[i]);

                    // 使用setString设置XML内容
                    pstmt.setString(3, xmlContent);

                    // 添加到批处理
                    pstmt.addBatch();

                    System.out.println("Added " + names[i] + " to batch");
                }

                // 执行批处理
                int[] updateCounts = pstmt.executeBatch();
                int totalAffected = java.util.Arrays.stream(updateCounts).sum();

                System.out.println("Batch insert completed, total affected: " + totalAffected);

                // 提交事务
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

### 更新XML数据

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

        // 加载驱动
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

            // 首先创建表并插入初始数据
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS XML_DATA1(id INT PRIMARY KEY, name VARCHAR(100), xml_content XMLTYPE)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // 插入初始数据（如果不存在）
            try {
                stmt.execute("DELETE FROM XML_DATA1 WHERE id = 1001");
                stmt.execute("INSERT INTO XML_DATA1 (id, name, xml_content) VALUES (1001, 'test', XMLPARSE(DOCUMENT '<product><name>servers</name></product>'))");
            } catch (SQLException e) {
                System.out.println("Insert initial data info: " + e.getMessage());
            }
            stmt.close();

            // 准备更新语句 - 使用XMLPARSE将字符串转换为XML
            String sql = "UPDATE XML_DATA1 SET xml_content = XMLPARSE(DOCUMENT ?) WHERE id = ?";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // 准备更新的XML内容
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

                // 设置参数
                pstmt.setString(1, updatedContent);
                pstmt.setInt(2, 1001);

                // 执行更新
                int affectedRows = pstmt.executeUpdate();
                System.out.println("Updated " + affectedRows + " row(s)");

                // 提交事务
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

### XML类型转换

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

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, info);

            // 查询并更新XML数据
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

### 删除XML数据

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

        // 加载驱动
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

            // 删除XML数据
            String sql = "DELETE FROM XML_DATA1 WHERE id = ?";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {

                // 删除ID为2000的记录
                pstmt.setInt(1, 2000); 

                int affectedRows = pstmt.executeUpdate();

                System.out.println("Deleted " + affectedRows + " record(s)");

                // 提交事务
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

### 流式XML处理

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

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, info);
            // 禁用自动提交
            conn.setAutoCommit(false); 

            // 先创建表（如果不存在）
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

            // 流式插入大XML文档
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

            // 插入到数据库
            String sql = "INSERT INTO XML_DATA1 (id, name, xml_content) VALUES (3000, 'stream_test', XMLPARSE(DOCUMENT ?))";

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
                pstmt.setString(1, xmlContent);

                int affectedRows = pstmt.executeUpdate();
                System.out.println("Streamed insert completed, affected rows: " + affectedRows);

                // 提交事务
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

## SQLXML数据源

### 从文件创建SQLXML

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.net.*;

public class SQLXMLFromFileExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        FileReader reader = null;
        SQLXML sqlXml = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 创建表（如果不存在）
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS config_data(id INTEGER, name VARCHAR(100), xml_content CLOB)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }
            stmt.close();

            // 读取XML文件内容
            String filePath = "C:/data/config.xml";
            StringBuilder xmlContent = new StringBuilder();

            // 创建示例XML文件（如果不存在）
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

            // 创建SQLXML对象
            sqlXml = conn.createSQLXML();
            sqlXml.setString(xmlContent.toString());

            // 插入到数据库
            String sql = "INSERT INTO config_data (id, name, xml_content) " +
                         "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(sql);

            pstmt.setInt(1, 2000); 
            // 名称 
            pstmt.setString(2, "system configuration"); 
            pstmt.setSQLXML(3, sqlXml);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted config data, affected rows: " + affectedRows);

            // 释放资源
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
            // 关闭资源
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

### 从URL创建SQLXML

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.io.*;
import java.net.*;

public class SQLXMLFromURLExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        InputStream is = null;
        SQLXML sqlXml = null;

        try {
            // 加载YashanDB JDBC驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 从URL读取XML
            String xmlUrl = "https://example.com/config.xml";

            // 创建示例XML内容（因为网络URL可能无法访问）
            String xmlContent = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<config><setting>sample</setting></config>";

            // 创建SQLXML对象
            sqlXml = conn.createSQLXML();
            sqlXml.setString(xmlContent);

            System.out.println("Created SQLXML from sample content");
            // 直接使用已设置的xmlContent

            // 插入到数据库
            String sql = "INSERT INTO config_data (id, name, xml_content) " +
                         "VALUES (?, ?, ?)";

            pstmt = conn.prepareStatement(sql);

            pstmt.setInt(1, 3000); 
            // 远程配置名称 
            pstmt.setString(2, "remote_config"); 
            pstmt.setSQLXML(3, sqlXml);

            int affectedRows = pstmt.executeUpdate();
            System.out.println("Inserted remote config, affected rows: " + affectedRows);

            // 释放资源
            if (sqlXml != null) {
                sqlXml.free();
            }

        
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            e.printStackTrace();

        // 捕获并处理SQLException异常
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // 关闭资源
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

