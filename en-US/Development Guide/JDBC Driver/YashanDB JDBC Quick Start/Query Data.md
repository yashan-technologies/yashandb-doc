This article provides a complete example demonstrating data query operations in tables based on YashanDB JDBC.

```java
package quickstart;

// TableQueryDataExample.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TableQueryDataExample {
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

    // Execute normal SQL statement to query data in the table.
    public static void queryData(Connection conn) {
        Statement stmt = null;
        ResultSet rs = null;
        try {
            stmt = conn.createStatement();

            // Execute query SQL statement.
            rs = stmt.executeQuery("SELECT id, name FROM customer WHERE id < 10");

            // Iterate through the result set.
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("id: " + id + ", name: " + name);
            }

            System.out.println("query table customer succeed!");

        } catch (SQLException e) {
            System.out.println("Query error: " + e.getMessage());
        } finally {
            // Close ResultSet
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
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

    // Close database connection.
    public static void closeConnection(Connection conn) {
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        // Get database connection
        Connection conn = getConnection("username", "password");
        if (conn != null) {
            // Query data
            queryData(conn);

            // Close database connection
            closeConnection(conn);
        }
    }
}
```

Compile in a Windows environment:

```shell
$ javac TableQueryDataExample.java
```

Execute:

```shell
$ java quickstart.TableQueryDataExample
Connection succeed!
id: 1, name: zhangsan
id: 2, name: lisi
id: 3, name: wangwu
...
query table customer succeed!
```
