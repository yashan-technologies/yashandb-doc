After all operations are executed and completed, the connection to the database must be closed.

The connection can be closed using the close method of the Connection object, as follows:

```java
conn.close();
```

> **Note**:
>
> The close method should typically be placed in a finally clause.

When closing database resources, the following order should be followed: First close ResultSet, then close Statement/PreparedStatement, and finally close Connection. This ensures that resources are properly released and avoids resource leaks.

```java
// Close PreparedStatement
if (pstmt != null) {
    try {
        pstmt.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
// Correct close order
if (rs != null) {
    rs.close();
}
if (stmt != null) {
    stmt.close();
}
if (conn != null) {
    conn.close();
}
```

The complete code example is as follows:

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Properties;

public class CloseResourcesExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            // Load YashanDB driver
            Class.forName("com.yashandb.jdbc.Driver");

            // Create database connection
            conn = DriverManager.getConnection(url, info);

            // Execute query
            String sql = "SELECT * FROM employees WHERE department = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, "IT");
            rs = pstmt.executeQuery();

            // Process results
            while (rs.next()) {
                System.out.println("Employee: " + rs.getString("employee_id"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close resources in the correct order
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
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