A transaction is the basic unit of database operations, ensuring that a set of operations either all succeed or all fail.

## Transaction Basic Concepts

Transactions follow ACID properties to ensure data consistency and reliability.

- **Atomicity**: All operations within a transaction complete completely or not at all.

- **Consistency**: Database is in a consistent state after transaction completion.

- **Isolation**: Concurrent executing transactions do not interfere with each other.

- **Durability**: After commit, modifications to database are permanently saved.

JDBC transfers transaction control to the database by disabling auto-commit, performing commit and rollback operations, and setting transaction isolation levels; the underlying database ensures the ACID properties of transactions.

### Transaction Commit Methods

The following is an example of JDBC transaction submission:

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class BasicTransactionExample {
    public static void main(String[] args) {
        // Database connection parameters
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

        try {
            conn = DriverManager.getConnection(url, info);

            // Create tables if not exist
            stmt = conn.createStatement();
            stmt.execute("CREATE TABLE IF NOT EXISTS employees(employee_id VARCHAR(10) PRIMARY KEY, department VARCHAR(50), salary DOUBLE)");
            stmt.execute("CREATE TABLE IF NOT EXISTS departments(department_id VARCHAR(10) PRIMARY KEY, department_name VARCHAR(100))");

            // Insert test data if not exist
            try {
                stmt.execute("INSERT INTO employees (employee_id, department, salary) VALUES ('E001', 'IT', 5000)");
            } catch (SQLException e) {
                // Ignore unique constraint conflict
            }
            try {
                stmt.execute("INSERT INTO departments (department_id, department_name) VALUES ('IT', 'Information Technology')");
            } catch (SQLException e) {
                // Ignore unique constraint conflict
            }

            // Turn off auto-commit
            conn.setAutoCommit(false);

            // Execute multiple SQL operations
            updateEmployee(conn, "E001", "IT", 10000);
            updateDepartment(conn, "IT", "Information Technology");

            // Commit transaction
            conn.commit();
            System.out.println("Transaction committed successfully");

        } catch (SQLException e) {
            // Rollback transaction
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
            // Close resources
            try {
                if (stmt != null) {
                    stmt.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                if (conn != null) {
                    // Restore auto-commit
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

## Transaction Isolation Levels

JDBC supports five transaction isolation levels, which provide APIs to control the visibility of concurrent transactions. YashanDB only supports READ_COMMITTED and SERIALIZABLE

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class IsolationLevelsExample {
    public static void main(String[] args) {
        // Database connection parameters
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

        // Demonstrate READ_COMMITTED isolation level
        try (Connection conn = DriverManager.getConnection(url, info)) {
            conn.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);
            System.out.println("READ_COMMITTED: Set to READ_COMMITTED");

            int isolationLevel = conn.getTransactionIsolation();
            System.out.println("Current isolation level: " + getIsolationLevelName(isolationLevel));
        } catch (SQLException e) {
            e.printStackTrace();
        }

        // Demonstrate SERIALIZABLE isolation level
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

### Isolation Level Examples

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class IsolationLevelExample {
    public static void main(String[] args) {
        // Database connection parameters
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

        // First connection
        try (Connection conn1 = DriverManager.getConnection(url, info)) {

            // Create table if not exists
            Statement createStmt = conn1.createStatement();
            try {
                createStmt.execute("CREATE TABLE IF NOT EXISTS accounts(id INT PRIMARY KEY, balance DOUBLE)");
                createStmt.execute("DELETE FROM accounts WHERE id = 1");
                createStmt.execute("INSERT INTO accounts (id, balance) VALUES (1, 1000)");
            } catch (SQLException e) {
                // Ignore table already exists error
            }
            createStmt.close();

            conn1.setAutoCommit(false);
            conn1.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);

            // Query data
            try (Statement stmt = conn1.createStatement();
                 ResultSet rs = stmt.executeQuery("SELECT balance FROM accounts WHERE id = 1")) {
                if (rs.next()) {
                    double balance = rs.getDouble("balance");
                    System.out.println("Initial balance: " + balance);
                }
            }

            // CountDownLatch for synchronization
            CountDownLatch latch = new CountDownLatch(1);

            // Another connection modifies data
            new Thread(() -> {
                try {
                    // Load driver
                    Class.forName("com.yashandb.jdbc.Driver");
                } catch (ClassNotFoundException ex) {
                    ex.printStackTrace();
                    return;
                }

                try (Connection conn2 = DriverManager.getConnection(url, info)) {
                    conn2.setAutoCommit(false);
                    try (Statement stmt = conn2.createStatement()) {
                        // This will block until the first connection commits or rolls back
                        int updated = stmt.executeUpdate(
                            "UPDATE accounts SET balance = balance - 100 WHERE id = 1");
                        conn2.commit();
                        System.out.println("Second connection committed");
                    }
                } catch (SQLException e) {
                    e.printStackTrace();
                } finally {
                    // Notify main thread
                    latch.countDown();
                }
            }).start();

            // Wait for child thread to complete
            try {
                latch.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // Query again
            try (Statement stmt = conn1.createStatement();
                 ResultSet rs = stmt.executeQuery("SELECT balance FROM accounts WHERE id = 1")) {
                if (rs.next()) {
                    double balance = rs.getDouble("balance");
                    // In READ_COMMITTED level, uncommitted changes are not visible
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

## Savepoints

Savepoints allow creating intermediate checkpoints within a transaction, enabling partial rollback operations.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SavepointExample {
    public static void main(String[] args) {
        // Database connection parameters
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

            // Create tables if not exist
            Statement stmt = conn.createStatement();
            stmt.execute("CREATE TABLE IF NOT EXISTS orders(order_id INT PRIMARY KEY, customer_id VARCHAR(10), amount DOUBLE)");
            stmt.execute("CREATE TABLE IF NOT EXISTS customers(customer_id VARCHAR(10) PRIMARY KEY, total_orders INT DEFAULT 0)");
            stmt.execute("CREATE TABLE IF NOT EXISTS inventory(product_id INT PRIMARY KEY, quantity INT)");

            // Insert test data if not exist
            try {
                stmt.execute("INSERT INTO customers (customer_id, total_orders) VALUES ('C001', 0)");
            } catch (SQLException e) {
                // Ignore table already exists error
            }
            stmt.close();

            conn.setAutoCommit(false);

            // Execute first operation
            try (Statement stmt1 = conn.createStatement()) {
                // Delete old data to avoid unique constraint conflicts
                stmt1.execute("DELETE FROM orders WHERE order_id = 1001");
                stmt1.executeUpdate("INSERT INTO orders (order_id, customer_id, amount) " +
                                 "VALUES (1001, 'C001', 500.00)");
                System.out.println("Added order 1001");
            }

            // Create first savepoint
            Savepoint savepoint1 = conn.setSavepoint("before_update");

            // Execute second operation
            try (Statement stmt2 = conn.createStatement()) {
                stmt2.executeUpdate("UPDATE customers SET total_orders = total_orders + 1 " +
                                 "WHERE customer_id = 'C001'");
                System.out.println("Updated customer C001");
            }

            // Create second savepoint
            Savepoint savepoint2 = conn.setSavepoint("before_error");

            //  Execute potentially failing operation
            try (Statement stmt3 = conn.createStatement()) {
                // This operation might fail
                stmt3.executeUpdate("INSERT INTO inventory (product_id, quantity) " +
                                 "VALUES (1001, -1)");
                // Assume this line throws an exception due to some reason
                throw new SQLException("Inventory cannot be negative");
            } catch (SQLException e) {
                System.out.println("Error occurred: " + e.getMessage());

                // Rollback to second savepoint
                conn.rollback(savepoint2);
                System.out.println("Rolled back to before_error savepoint");

                // Note: Cannot call releaseSavepoint on a rolled back savepoint

                // Check if operations were rolled back
                checkData(conn);

                // Commit all operations before first savepoint
                conn.commit();
                System.out.println("Transaction committed");
            }

        } catch (SQLException e) {
            e.printStackTrace();
            // Rollback entire transaction
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

## Distributed Transactions

XADataSource and XAConnection are used for distributed transaction processing.

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
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";

        // Load driver
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
            // Create XA data source
            YasXADataSource xaDataSource = new YasXADataSource();
            xaDataSource.setURL(url);
            xaDataSource.setUser(user);
            xaDataSource.setPassword(password);

            // Get XA connection
            xaConnection = xaDataSource.getXAConnection();
            conn = xaConnection.getConnection();
            XAResource xaResource = xaConnection.getXAResource();

            // Disable auto-commit
            conn.setAutoCommit(false);

            // Create test table if not exists
            stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS xa_accounts(id INT PRIMARY KEY, balance DOUBLE)");
                stmt.execute("DELETE FROM xa_accounts WHERE id = 1");
                stmt.execute("INSERT INTO xa_accounts (id, balance) VALUES (1, 1000)");
                conn.commit();
            } catch (SQLException e) {
            }

            // Print initial balance
            try (ResultSet rs = stmt.executeQuery("SELECT balance FROM xa_accounts WHERE id = 1")) {
                if (rs.next()) {
                    System.out.println("Initial balance: " + rs.getDouble(1));
                }
            }

            // Start distributed transaction
            Xid xid = new TestXid(1, 1, "test".getBytes());
            xaResource.start(xid, XAResource.TMNOFLAGS);

            // Execute SQL operation
            stmt.executeUpdate("UPDATE xa_accounts SET balance = balance - 100 WHERE id = 1");
            System.out.println("Deducted 100 from account 1");

            // End transaction
            xaResource.end(xid, XAResource.TMSUCCESS);

            // Commit transaction
            xaResource.commit(xid, true);
            System.out.println("Transaction committed successfully");

            // Verify result
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
            // Close resources
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

    // Simple Xid implementation
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

