事务是数据库操作的基本单元，确保一组操作要么全部成功，要么全部失败。

## 事务基础概念

事务遵循ACID特性，保证数据的一致性和可靠性。

- **原子性**：事务中的所有操作要么全部完成，要么全部不完成。

- **一致性**：事务完成后数据库处于一致状态。

- **隔离性**：并发执行的事务互不干扰。

- **持久性**：事务提交后，对数据库的修改永久保存。

JDBC通过关闭自动提交、执行提交与回滚操作，并设置事务隔离级别，将事务控制权交由数据库，由数据库底层保障事务的ACID特性。

### 事务提交方法

JDBC提交事务示例如下：

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class BasicTransactionExample {
    public static void main(String[] args) {
        // 数据库连接参数
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

        try {
            conn = DriverManager.getConnection(url, info);

            // 创建表（如果不存在）
            stmt = conn.createStatement();
            stmt.execute("CREATE TABLE IF NOT EXISTS employees(employee_id VARCHAR(10) PRIMARY KEY, department VARCHAR(50), salary DOUBLE)");
            stmt.execute("CREATE TABLE IF NOT EXISTS departments(department_id VARCHAR(10) PRIMARY KEY, department_name VARCHAR(100))");

            // 插入测试数据（如果不存在）
            try {
                stmt.execute("INSERT INTO employees (employee_id, department, salary) VALUES ('E001', 'IT', 5000)");
            } catch (SQLException e) {
                // 忽略唯一约束冲突
            }
            try {
                stmt.execute("INSERT INTO departments (department_id, department_name) VALUES ('IT', 'Information Technology')");
            } catch (SQLException e) {
                // 忽略唯一约束冲突
            }

            // 关闭自动提交
            conn.setAutoCommit(false);

            // 执行多个SQL操作
            updateEmployee(conn, "E001", "IT", 10000);
            updateDepartment(conn, "IT", "Information Technology");

            // 提交事务
            conn.commit();
            System.out.println("Transaction committed successfully");

        } catch (SQLException e) {
            // 回滚事务
            try {
                if (conn != null) {
                    conn.rollback();
                    System.out.println("Transaction rolled back");
                }
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    // 恢复自动提交
                    conn.setAutoCommit(true);
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    private static void updateEmployee(Connection conn, String empId, String dept, double salary)
            throws SQLException {
        String sql = "UPDATE employees SET department = ?, salary = ? WHERE employee_id = ?";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, dept);
            pstmt.setDouble(2, salary);
            pstmt.setString(3, empId);
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " employee record(s)");
        }
    }

    private static void updateDepartment(Connection conn, String deptId, String deptName)
            throws SQLException {
        String sql = "UPDATE departments SET department_name = ? WHERE department_id = ?";
        try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, deptName);
            pstmt.setString(2, deptId);
            int affectedRows = pstmt.executeUpdate();
            System.out.println("Updated " + affectedRows + " department record(s)");
        }
    }
}
```

## 事务隔离级别

JDBC支持五种事务隔离级别，提供了对应的隔离级别API接口用于控制并发事务之间的可见性。YashanDB只支持READ_COMMITTED和SERIALIZABLE。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class IsolationLevelsExample {
    public static void main(String[] args) {
        // 数据库连接参数
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

        // 演示 READ_COMMITTED 隔离级别
        try (Connection conn = DriverManager.getConnection(url, info)) {
            conn.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);
            System.out.println("READ_COMMITTED: Set to READ_COMMITTED");

            int isolationLevel = conn.getTransactionIsolation();
            System.out.println("Current isolation level: " + getIsolationLevelName(isolationLevel));
        } catch (SQLException e) {
            e.printStackTrace();
        }

        // 演示 SERIALIZABLE 隔离级别
        try (Connection conn = DriverManager.getConnection(url, info)) {
            conn.setTransactionIsolation(Connection.TRANSACTION_SERIALIZABLE);
            System.out.println("SERIALIZABLE: Set to SERIALIZABLE");

            int isolationLevel = conn.getTransactionIsolation();
            System.out.println("Current isolation level: " + getIsolationLevelName(isolationLevel));
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static String getIsolationLevelName(int level) {
        switch (level) {
            case Connection.TRANSACTION_NONE:
                return "TRANSACTION_NONE";
            case Connection.TRANSACTION_READ_UNCOMMITTED:
                return "READ_UNCOMMITTED";
            case Connection.TRANSACTION_READ_COMMITTED:
                return "READ_COMMITTED";
            case Connection.TRANSACTION_REPEATABLE_READ:
                return "REPEATABLE_READ";
            case Connection.TRANSACTION_SERIALIZABLE:
                return "SERIALIZABLE";
            default:
                return "UNKNOWN";
        }
    }
}
```

### 隔离级别示例

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class IsolationLevelExample {
    public static void main(String[] args) {
        // 数据库连接参数
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

        // 第一个连接
        try (Connection conn1 = DriverManager.getConnection(url, info)) {

            // 创建表（如果不存在）
            Statement createStmt = conn1.createStatement();
            try {
                createStmt.execute("CREATE TABLE IF NOT EXISTS accounts(id INT PRIMARY KEY, balance DOUBLE)");
                createStmt.execute("DELETE FROM accounts WHERE id = 1");
                createStmt.execute("INSERT INTO accounts (id, balance) VALUES (1, 1000)");
            } catch (SQLException e) {
                // 忽略表已存在错误
            }
            createStmt.close();

            conn1.setAutoCommit(false);
            conn1.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);

            // 查询数据
            try (Statement stmt = conn1.createStatement();
                 ResultSet rs = stmt.executeQuery("SELECT balance FROM accounts WHERE id = 1")) {
                if (rs.next()) {
                    double balance = rs.getDouble("balance");
                    System.out.println("Initial balance: " + balance);
                }
            }

            // 用于同步的CountDownLatch
            CountDownLatch latch = new CountDownLatch(1);

            // 另一个连接修改数据
            new Thread(() -> {
                try {
                    // 加载驱动
                    Class.forName("com.yashandb.jdbc.Driver");
                } catch (ClassNotFoundException ex) {
                    ex.printStackTrace();
                    return;
                }

                try (Connection conn2 = DriverManager.getConnection(url, info)) {
                    conn2.setAutoCommit(false);
                    try (Statement stmt = conn2.createStatement()) {
                        // 这里会阻塞，直到第一个连接提交或回滚
                        int updated = stmt.executeUpdate(
                            "UPDATE accounts SET balance = balance - 100 WHERE id = 1");
                        conn2.commit();
                        System.out.println("Second connection committed");
                    }
                } catch (SQLException e) {
                    e.printStackTrace();
                } finally {
                    // 通知主线程
                    latch.countDown();
                }
            }).start();

            // 等待子线程完成
            try {
                latch.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // 再次查询
            try (Statement stmt = conn1.createStatement();
                 ResultSet rs = stmt.executeQuery("SELECT balance FROM accounts WHERE id = 1")) {
                if (rs.next()) {
                    double balance = rs.getDouble("balance");
                    // 在READ_COMMITTED级别，不会看到未提交的修改
                    System.out.println("Balance after update: " + balance);
                }
            }

            conn1.commit();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 保存点

保存点允许在事务中创建中间检查点，可以部分回滚操作。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SavepointExample {
    public static void main(String[] args) {
        // 数据库连接参数
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

            // 创建表（如果不存在）
            Statement stmt = conn.createStatement();
            stmt.execute("CREATE TABLE IF NOT EXISTS orders(order_id INT PRIMARY KEY, customer_id VARCHAR(10), amount DOUBLE)");
            stmt.execute("CREATE TABLE IF NOT EXISTS customers(customer_id VARCHAR(10) PRIMARY KEY, total_orders INT DEFAULT 0)");
            stmt.execute("CREATE TABLE IF NOT EXISTS inventory(product_id INT PRIMARY KEY, quantity INT)");

            // 插入测试数据（如果不存在）
            try {
                stmt.execute("INSERT INTO customers (customer_id, total_orders) VALUES ('C001', 0)");
            } catch (SQLException e) {
                // 忽略唯一约束冲突
            }
            stmt.close();

            conn.setAutoCommit(false);

            // 执行第一个操作
            try (Statement stmt1 = conn.createStatement()) {
                // 删除旧数据避免唯一约束冲突
                stmt1.execute("DELETE FROM orders WHERE order_id = 1001");
                stmt1.executeUpdate("INSERT INTO orders (order_id, customer_id, amount) " +
                                 "VALUES (1001, 'C001', 500.00)");
                System.out.println("Added order 1001");
            }

            // 创建第一个保存点
            Savepoint savepoint1 = conn.setSavepoint("before_update");

            // 执行第二个操作
            try (Statement stmt2 = conn.createStatement()) {
                stmt2.executeUpdate("UPDATE customers SET total_orders = total_orders + 1 " +
                                 "WHERE customer_id = 'C001'");
                System.out.println("Updated customer C001");
            }

            // 创建第二个保存点
            Savepoint savepoint2 = conn.setSavepoint("before_error");

            // 执行可能失败的操作
            try (Statement stmt3 = conn.createStatement()) {
                // 这个操作可能会失败
                stmt3.executeUpdate("INSERT INTO inventory (product_id, quantity) " +
                                 "VALUES (1001, -1)");
                // 假设这行代码因为某些原因抛出异常
                throw new SQLException("Inventory cannot be negative");
            } catch (SQLException e) {
                System.out.println("Error occurred: " + e.getMessage());

                // 回滚到第二个保存点
                conn.rollback(savepoint2);
                System.out.println("Rolled back to before_error savepoint");

                // 注意：不能对已回滚的保存点调用releaseSavepoint

                // 检查是否操作是否被撤销
                checkData(conn);

                // 提交到第一个保存点之前的所有操作
                conn.commit();
                System.out.println("Transaction committed");
            }

        } catch (SQLException e) {
            e.printStackTrace();
            // 回滚整个事务
            if (conn != null) {
                try {
                    conn.rollback();
                    System.out.println("Full transaction rolled back");
                } catch (SQLException ex) {
                    ex.printStackTrace();
                }
            }
        } finally {
            if (conn != null) {
                try {
                    conn.setAutoCommit(true);
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    private static void checkData(Connection conn) throws SQLException {
        try (Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery("SELECT total_orders FROM customers WHERE customer_id = 'C001'")) {
            if (rs.next()) {
                int orders = rs.getInt("total_orders");
                System.out.println("Customer C001 total orders: " + orders);
            }
        }
    }
}
```

## 分布式事务

XADataSource和XAConnection用于分布式事务处理。

```java
package quickstart;

import javax.sql.XADataSource;
import javax.sql.XAConnection;
import javax.transaction.xa.XAResource;
import javax.transaction.xa.XAException;
import javax.transaction.xa.Xid;

import com.yashandb.xa.YasXADataSource;
import java.sql.Connection;
import java.sql.Statement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class XATransactionExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        XAConnection xaConnection = null;
        Connection conn = null;
        Statement stmt = null;

        try {
            // 创建XA数据源
            YasXADataSource xaDataSource = new YasXADataSource();
            xaDataSource.setURL(url);
            xaDataSource.setUser(user);
            xaDataSource.setPassword(password);

            // 获取XA连接
            xaConnection = xaDataSource.getXAConnection();
            conn = xaConnection.getConnection();
            XAResource xaResource = xaConnection.getXAResource();

            // 禁用自动提交
            conn.setAutoCommit(false);

            // 创建测试表（如果不存在）
            stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS xa_accounts(id INT PRIMARY KEY, balance DOUBLE)");
                stmt.execute("DELETE FROM xa_accounts WHERE id = 1");
                stmt.execute("INSERT INTO xa_accounts (id, balance) VALUES (1, 1000)");
                conn.commit();
            } catch (SQLException e) {
                // 忽略表已存在错误

            // 打印初始余额
            try (ResultSet rs = stmt.executeQuery("SELECT balance FROM xa_accounts WHERE id = 1")) {
                if (rs.next()) {
                    System.out.println("Initial balance: " + rs.getDouble(1));
                }
            }

            // 开始分布式事务
            Xid xid = new TestXid(1, 1, "test".getBytes());
            xaResource.start(xid, XAResource.TMNOFLAGS);

            // 执行SQL操作
            stmt.executeUpdate("UPDATE xa_accounts SET balance = balance - 100 WHERE id = 1");
            System.out.println("Deducted 100 from account 1");

            // 结束事务
            xaResource.end(xid, XAResource.TMSUCCESS);

            // 提交事务
            xaResource.commit(xid, true);
            System.out.println("Transaction committed successfully");

            // 验证结果
            try (ResultSet rs = stmt.executeQuery("SELECT balance FROM xa_accounts WHERE id = 1")) {
                if (rs.next()) {
                    System.out.println("New balance: " + rs.getDouble(1));
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } catch (XAException e) {
            System.out.println("XA Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // 关闭资源
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
            try {
                if (xaConnection != null) {
                    xaConnection.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    // 简单的Xid实现
    static class TestXid implements Xid {
        private int formatId;
        private int gtrid;
        private byte[] bqual;

        public TestXid(int formatId, int gtrid, byte[] bqual) {
            this.formatId = formatId;
            this.gtrid = gtrid;
            this.bqual = bqual;
        }

        @Override
        public int getFormatId() {
            return formatId;
        }

        @Override
        public byte[] getGlobalTransactionId() {
            return new byte[]{(byte) gtrid};
        }

        @Override
        public byte[] getBranchQualifier() {
            return bqual;
        }
    }
}
```

