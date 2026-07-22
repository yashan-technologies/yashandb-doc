ResultSet对象表示数据库查询返回的结果集。它是一个可滚动、可更新的结果集，可以用来遍历查询结果。

## ResultSet使用

### 创建ResultSet

ResultSet创建示例。

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class ResultSetDemo {
    // 数据库连接参数，替换为自己的配置
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {
        // 声明资源对象：Connection/Statement/ResultSet
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(URL, USER, PASSWORD);

            // 创建Statement对象，通过Connection createStatement()方法创建
            stmt = conn.createStatement();

            // 示例：执行插入
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            // 执行插入，返回受影响行数
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);

            // 示例：执行查询
            String querySql = "SELECT id, name FROM customer WHERE id = 2";

            // 执行查询，返回结果集
            rs = stmt.executeQuery(querySql);

            // 处理结果集
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("Query Result: ID=" + id + ", Name=" + name);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## ResultSet基础操作 

### 遍历结果集 

ResultSet提供了next()方法来移动游标到下一行数据。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetBasicsExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);
            // 创建Statement对象
            stmt = conn.createStatement();
            // 执行查询
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // 遍历结果集
            while (rs.next()) {
                // 通过列索引获取数据
                int id = rs.getInt(1);
                // 通过列名获取数据
                String name = rs.getString("NAME");

                System.out.println("ID: " + id + ", Name: " + name);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

### 检索数据 

ResultSet提供了多种get方法来获取不同类型的数据。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetDataTypesExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);
            // 创建Statement对象
            stmt = conn.createStatement();
            // 执行查询
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            while (rs.next()) {
                // 获取不同类型的数据
                // 整数类型
                int id = rs.getInt("ID");
                // 字符串类型
                String name = rs.getString("NAME");

                System.out.println("ID: " + id + ", Name: " + name);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

## ResultSet高级功能 

### 可滚动结果集 

通过指定ResultSet类型，可以实现前后滚动、绝对定位等高级功能。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ScrollableResultSetExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);
            // 创建可滚动但不敏感的结果集
            stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,
                ResultSet.CONCUR_READ_ONLY);

            // 执行查询
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // 移动到最后一行
            rs.last();
            System.out.println("Total rows: " + rs.getRow());

            // 移动到第一行
            rs.first();
            System.out.println("First employee: " + rs.getString("NAME"));

            // 移动到特定行
            rs.absolute(5);
            System.out.println("5th employee: " + rs.getString("NAME"));

            // 相对移动
            rs.relative(-2);
            System.out.println("3rd employee: " + rs.getString("NAME"));

            // 移动到上一行
            while (rs.previous()) {
                System.out.println("Employee: " + rs.getString("NAME"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

### 可更新结果集 

通过指定并发类型，可以创建可更新的结果集，直接修改数据库中的数据。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class UpdatableResultSetExample {
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
            conn.setAutoCommit(false);

            // 创建可更新的结果集
            stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,
                ResultSet.CONCUR_UPDATABLE);

            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // 遍历结果集并更新数据
            while (rs.next()) {
                String name = rs.getString("NAME");

              
                String newName = name;
                newName = name + "_upd";
                rs.updateString("NAME", newName);
                // 更新数据库
                rs.updateRow();
                System.out.println("Updated name: " + name);
            }

            conn.commit();

        } catch (SQLException e) {
            e.printStackTrace();
            if (conn != null) {
                try {
                    conn.rollback();
                } catch (SQLException ex) {
                    ex.printStackTrace();
                }
            }
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

## ResultSet元数据 

ResultSetMetaData提供了结果集的结构信息，包括列数、列名、列类型等。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetMetaDataExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);
            // 创建Statement对象
            stmt = conn.createStatement();
            // 执行查询
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // 获取结果集元数据
            ResultSetMetaData metaData = rs.getMetaData();

            // 获取列数
            int columnCount = metaData.getColumnCount();
            System.out.println("Total columns: " + columnCount);
            System.out.println("----------------------------------------");

            // 遍历每一列的元数据
            for (int i = 1; i <= columnCount; i++) {
                // 列名
                String columnName = metaData.getColumnName(i);
                // 列标签
                String columnLabel = metaData.getColumnLabel(i);
                // 列类型
                int columnType = metaData.getColumnType(i);
                // 类型名称
                String typeName = metaData.getColumnTypeName(i);
                // 列大小
                int columnSize = metaData.getColumnDisplaySize(i);
                // 是否可为空
                int nullable = metaData.isNullable(i);

                System.out.println("Column " + i + ":");
                System.out.println("  Name: " + columnName);
                System.out.println("  Label: " + columnLabel);
                System.out.println("  Type: " + columnType + " (" + typeName + ")");
                System.out.println("  Size: " + columnSize);
                System.out.println("  Nullable: " + (nullable == ResultSetMetaData.columnNullable ? "Yes" : "No"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

## ResultSet性能优化 

确保ResultSet正确关闭，避免资源泄漏。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResourceManagementExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);
            // 创建Statement对象
            stmt = conn.createStatement();
            // 执行查询
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // 处理结果集
            while (rs.next()) {
                System.out.println("Customer: " + rs.getString("NAME"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

