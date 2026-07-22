所有操作执行完成后，必须关闭与数据库的连接。

可以使用Connection对象的close方法关闭连接，如下所示：

```java
conn.close();
```

> **Note**:
>
> 通常应将close放在finally子句中。

关闭数据库资源时，应按照以下顺序进行：先关闭ResultSet，然后关闭Statement/PreparedStatement，最后关闭Connection。这样可以确保资源被正确释放，避免资源泄露。

```java
// 关闭PreparedStatement
if (pstmt != null) {
    try {
        pstmt.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
// 正确的关闭顺序
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

完整的代码示例如下：

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
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            // 加载YashanDB数据库驱动
            Class.forName("com.yashandb.jdbc.Driver");

            // 创建数据库连接
            conn = DriverManager.getConnection(url, info);

            // 执行查询
            String sql = "SELECT * FROM employees WHERE department = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, "IT");
            rs = pstmt.executeQuery();

            // 处理结果
            while (rs.next()) {
                System.out.println("Employee: " + rs.getString("employee_id"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 按照正确的顺序关闭资源
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