TAF is a client-side functionality that minimizes the disruption to end-user applications when database connections fail due to instance or network issues.

## URL Configuration Parameters

The following URL parameters related to the TAF functionality exist in YashanDB JDBC:

|Parameter Name |Data Type |Parameter Description |
| ----------------- | --------------------- |--------------------------------------------|
| failover            | string    | Whether to enable TAF. The value can be ON or OFF, default is OFF.<br />* ON: Indicates TAF is enabled, and the application will automatically switch to the other configured database nodes in case of a failure.<br />* OFF: Indicates TAF is disabled, and automatic reconnection to the current database node is not supported in OFF state. |
| failoverType        | string    | Type of Failover, supports NONE, SESSION, and SELECT, default is NONE.<br />* NONE: Indicates no Failover is used.<br />* SESSION: Indicates reconnecting and resetting the Statement handle after a failure.<br />* SELECT: Indicates re-executing the failed SELECT statement (the SELECT statement cannot be a query with LOB-type binding parameters) after switching the connection, but it does not support secondary operations on previously obtained objects.                      |
| failoverMethod      | string    | The method of Failover, which will determine the speed of Failover from the primary node to the standby node.<br/>* BASIC: Indicates that a new connection is established during Failover. |
| failoverRetries     | int       | Number of retry attempts, default value is 5.                                |
| failoverDelay       | int       | Time interval for retries (in seconds), default value is 1.                    |

The format for URL parameters is:

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/yashan?key1=value1&key2=value2
```

Example of URL connection configuration:

```java
jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,192.168.1.5:1688;192.168.1.6:1688,192.168.1.7:1688/yashan?poolTimeout=180&failover=on&failoverType=session&failoverMethod=basic&failoverRetries=5&failoverDelay=1
```

When configuring [multiple groups of IP/PORT connections](../YashanDB JDBC Driver Usage Introduction.html#IPs), connections are made within a group according to the serverType, and between groups based on priority order. For example, if there are three groups of IP/PORT connections and a TAF event occurs on a node in the second group, it will first try other nodes in the second group, then access nodes in the first group, and finally nodes in the third group.

## TAF Related Interfaces

The interfaces related to TAF functionality are as follows:

|Class |Return Type |Method |Parameters |Description |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| YasConnection       | void        | registerTAFCallback(YasFailover yasFailover, Object ctxt) | \[1\] yasFailover: An entity class registering the implementation of callbackFn method<br/>\[2\] ctxt: Any object the user wishes to save<br/> | Registers the implementing class of the TAF callback function. If a callback function is registered, the failoverRetries and failoverDelay parameters will not take effect, and no reconnection attempts will occur upon TAF link failure. |
| YasFailover        | void        | callbackFn (Connection conn, Object ctxt, int type, int event) | \[1\] conn: The current connection<br/>\[2\] ctxt: Any object the user wishes to save<br/>\[3\] type: Failover type<br/>\[4\] event: Event<br/> | TAF callback function, this method is implemented by the application. Currently only FO_BEGIN, FO_ERROR, FO_RETRY, FO_END are supported. In the FO_ERROR event, if FO_RETRY is returned, it indicates to attempt connecting again. |

## Usage Example

When a database server fails, a JDBC driver configured with TAF functionality can automatically reconnect or switch to an active instance. A successful switch may throw related exceptions, which developers can capture and analyze to determine if further action is needed (e.g. roll back a transaction).

- Exception message "The transaction must be rerun." indicates that TAF was triggered within the current operation and reconnection was successful, but the current operation is in a transaction process and the server-side transaction has been rolled back. At this point, the application layer needs to execute connection.rollback() and re-execute the transaction.  

- Exception message "Cannot be safely called repeatedly." indicates that TAF was triggered within the current operation and reconnection was successful. At this point, the application layer needs to re-execute the SQL ensuring safety.  

- Exception message "Failover reconnect fail" indicates that TAF was triggered within the current operation, but reconnection failed. At this point, the network should be checked for normal operation and the database should be checked for proper running.  

Example of TAF occurring during a transaction:

```java

    public static void main(String[] args) throws Exception {

        String className = "com.yashandb.jdbc.Driver";
        String url = "jdbc:yasdb://192.168.1.2:1688,192.168.1.3:1688/test?failover=on&failoverType=session&failoverMethod=basic&failoverRetries=100&failoverDelay=15";
        String user = "user"; // Username
        String password = "password"; // Password
        Class.forName(className);
        Connection connection = DriverManager.getConnection(url, user, password); // Get connection
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
        String user = "user"; // Username
        String password = "password"; // Password
        Class.forName(className);
        Connection connection = DriverManager.getConnection(url, user, password); // Get connection
        connection.setAutoCommit(false); // Not starting a transaction
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
