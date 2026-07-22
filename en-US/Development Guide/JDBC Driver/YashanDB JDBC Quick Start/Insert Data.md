This article provides a complete example demonstrating batch data insertion into tables based on YashanDB JDBC.    

```java
package quickstart;

// TableInsertDataExample.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;

public class TableInsertDataExample {
    // Create database connection.
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Connection conn = null;

        // Load database driver.
        try {
            Class.forName(driver);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }

        try {
            // Create database connection.
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }

    // Execute normal SQL statement to create customer table.
    public static void createTable(Connection conn) {
        Statement stmt = null;
        try {
            stmt = conn.createStatement();

            // Execute normal SQL statement, use IF NOT EXISTS to avoid duplicate creation error
            stmt.execute("CREATE TABLE IF NOT EXISTS customer(id INTEGER, name VARCHAR(32))");
            System.out.println("create table customer succeed!");

        } catch (SQLException e) {
            System.out.println("Table info: " + e.getMessage());
        } finally {
            // Close Statement
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    // Execute prepared statement to batch insert data.
    public static void batchInsertData(Connection conn) {
        PreparedStatement pst = null;

        try {
            // Generate prepared statement.
            pst = conn.prepareStatement("INSERT INTO customer VALUES (?,?)");
            for (int i = 0; i < 3; i++) {
                // Add parameters.
                pst.setInt(1, i);
                pst.setString(2, "sales" + i);
                pst.addBatch();
            }
            // Execute batch.
            pst.executeBatch();
            System.out.println("insert table customer succeed!");

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close PreparedStatement
            if (pst != null) {
                try {
                    pst.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static int execJdbcexample() {
        // Create database connection.
        Connection conn = getConnection("sales", "sales");

        if (conn == null) {
            return 0;
        }

        // Batch insert data.
        batchInsertData(conn);

        // Close database connection.
        try {
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
            return 0;
        }
        return 1;
    }

    /**
     * Main program, calling each static method step by step.
     * @param args
     */
    public static void main(String[] args) {
        int result = execJdbcexample();
    }
}
```

Compile in a Windows environment:

```shell
$ javac TableCreateExample.java
```

Execute:

```shell
$ java quickstart.TableInsertDataExample
Connection succeed!
create table customer succeed!
insert table customer succeed!
```
