TAF is a client-side functionality that minimizes the disruption to end-user applications when database connections fail due to instance or network issues.

Using TAF-related functions requires configuring some parameters during database connection. Please refer to [Configuring High Availability Connections](../YashanDB JDBC Advanced Configuration/Configuring High Availability Connections) for details.

## TAF Related Interfaces

The interfaces related to TAF functionality are as follows:

|Class  |Return Type |Method                                              |Parameters                                          |Description                                         |
| ------------- | ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| YasConnection | void                     | registerTAFCallback(YasFailover yasFailover, Object ctxt)    | \[1\] yasFailover: An entity class registering the implementation of callbackFn method<br/>\[2\] ctxt: Any object the user wishes to save<br/> | Registers the implementing class of the TAF callback function. If a callback function is registered, the failoverRetries and failoverDelay parameters will not take effect, and no reconnection attempts will occur upon TAF link failure. |
| YasFailover   | void                     | callbackFn (Connection conn, Object ctxt, int type, int event) | \[1\] conn: The current connection<br/>\[2\] ctxt: Any object the user wishes to save<br/>\[3\] type: Failover type<br/>\[4\] event: Failover event type<br/> | TAF callback function, this method is implemented by the application. Currently only FO_BEGIN, FO_ERROR, FO_RETRY, FO_END are supported. In the FO_ERROR event, if FO_RETRY is returned, it indicates to attempt connecting again. |

## Usage Example

When a database server fails, a JDBC driver configured with TAF functionality can automatically reconnect or switch to an active instance. A successful switch may throw related exceptions, which developers can capture and analyze to determine if further action is needed (e.g. roll back a transaction).

- Exception message "The transaction must be rerun." indicates that TAF was triggered within the current operation and reconnection was successful, but the current operation is in a transaction process and the server-side transaction has been rolled back. At this point, the application layer needs to execute connection.rollback() and re-execute the transaction.  

- Exception message "Cannot be safely called repeatedly." indicates that TAF was triggered within the current operation and reconnection was successful. At this point, the application layer needs to re-execute the SQL ensuring safety.  

- Exception message "Failover reconnect fail" indicates that TAF was triggered within the current operation, but reconnection failed. At this point, the network should be checked for normal operation and the database should be checked for proper running.  

Example of TAF occurring during a transaction:

```java
import java.sql.*;
import java.util.Properties;

    public static void main(String[] args) throws Exception {

        String className = "com.yashandb.jdbc.Driver";
        String url = "jdbc:yasdb://192.168.1.2:1688,192.168.1.3:1688/test?failover=on&failoverType=session&failoverMethod=basic&failoverRetries=100&failoverDelay=15";
        Properties info = new Properties();
        info.setProperty("user", "user");
        info.setProperty("password", "password");
        Class.forName(className);
        Connection connection = DriverManager.getConnection(url, info); // Get connection
        connection.setAutoCommit(false); // Start transaction
        dealTafInTransaction(connection);
    }
    
    private static void dealTafInTransaction(Connection connection) throws SQLException {
        try (Statement statement = connection.createStatement()) {
            // sql1, sql2, sql3 represent the SQL statements to be executed within the transaction, specific content not listed here
            String sql1 = "...";
            String sql2 = "...";
            String sql3 = "...";
            try {
                statement.execute(sql1);
                statement.execute(sql2);
                statement.execute(sql3);
                connection.commit(); // Commit transaction
            } catch (SQLException e) {
                connection.rollback(); // Must execute rollback; otherwise any statement or commit operation will result in an error
                if ("The transaction must be rerun.".equals(e.getMessage())) { // If this exception message appears, it indicates TAF has successfully reconnected, at this time the entire transaction can be re-executed if desired
                    dealTafInTransaction(connection);
                }
                throw e;
            }
        }
    }
```

Example of TAF occurring outside a transaction:

```java

    private static final String TAF_ERROR_MSG = "Cannot be safely called repeatedly.";

    public static void main(String[] args) throws Exception {

        String className = "com.yashandb.jdbc.Driver";
        String url = "jdbc:yasdb://192.168.1.2:1688,192.168.1.3:1688/test?failover=on&failoverType=session&failoverMethod=basic&failoverRetries=100&failoverDelay=15";
        Properties info = new Properties();
        info.setProperty("user", "user");
        info.setProperty("password", "password");
        Class.forName(className);
        Connection connection = DriverManager.getConnection(url, info); // Get connection
        connection.setAutoCommit(true); // Not starting a transaction
        dealTafWithoutTransaction(connection);
    }

    public static void dealTafWithoutTransaction(Connection connection) throws SQLException {
        try (Statement statement = connection.createStatement()) {
            // sql1, sql2 represent the SQL statements to be executed, specific content not listed here
            String sql1 = "...";
            String sql2 = "...";

            try {
                statement.execute(sql1);
            } catch (SQLException e) {
                if (TAF_ERROR_MSG.equals(e.getMessage())) { // If this exception message appears, it indicates TAF has successfully reconnected, at this time the statement can be re-executed
                    statement.execute(sql1);
                }
                throw e;
            }

            // For parameterized SQL statements (assuming sql2 is a parameterized SQL statement), if TAF occurs during execution, no need to rebind the parameters
            PreparedStatement preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.setInt(1,1);
            preparedStatement.setString(2, "2"); // Parameter binding
            try {
                preparedStatement.execute();
            } catch (SQLException e) {
                if (TAF_ERROR_MSG.equals(e.getMessage())) { // If TAF occurs during the execution of the SQL statement, just re-execute the statement without rebinding the parameters
                    preparedStatement.execute();
                }
                throw e;
            }
        }
    }
```
