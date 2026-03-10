### Connection Methods 

The YashanDB JDBC driver provides two methods to establish a database connection.

#### DriverManager Connection

Database connections can be established through the following three DriverManager methods:

*   DriverManager.getConnection(String url);
*   DriverManager.getConnection(String url, Properties info);
*   DriverManager.getConnection(String url, String user, String password);

#### YasDataSource Connection

A database connection can be established through the YasDataSource class provided by YashanDB:

*   YasDataSource ads = new YasDataSource();

#### Parameter Description

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| url       | Database connection descriptor, formatted as jdbc:yasdb://host:port/database_name<br>\*   host:port: The database's listening address, where host is the server IP address or domain name. If host is omitted, it means connecting to localhost; port is the database service port, for example 1688. <br>\*   database_name: Database name, mandatory. This parameter is for compatibility only, has no actual meaning, and does not validate the parameter value. |
| user      | Database username. |
| password  | Database user password. |
| info      | Database connection attributes. |

***Example***

```java
//DriverManager method
public static Connection getConnection(String username, String passwd) {
        //Driver class.
        String driver = "com.yashandb.jdbc.Driver";
        //Database connection descriptor.
        String sourceURL = "jdbc:yasdb://10.10.10.2:1688/yasdb";
        Connection conn = null;
         
        try {
            //Load driver.
            Class.forName(driver);
        } catch( Exception e ) {
            e.printStackTrace();
            return null;
        }
         
        try {
             //Create connection.
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch(Exception e) {
            e.printStackTrace();
            return null;
        }
         
        return conn;
    }
 
//YasDataSource method
public static Connection getConnDS() {
    Connection connect = null;
    try {
      YasDataSource ads = new YasDataSource();
      ads.setURL(url);
      connect = ads.getConnection(user, password);
    } catch (Exception e) {
      e.printStackTrace();
    }
    return connect;
  }
```

### Special Connection Formats

The URL described above is the basic connection configuration format. The YashanDB JDBC driver also supports some special connection format configurations.

#### Timeout Configuration

The YashanDB JDBC driver has established a timeout processing mechanism, allowing configuration of timeout durations for the program to exit gracefully at the specified deadline.

The configurable timeout durations and descriptions are listed in the following table:

|Timeout Duration |Description |
| --------------- | ------------------------------------------------------------ |
| connectTimeout                          | Timeout for creating the socket connection when establishing a connection, Unit: seconds. Default is 10 seconds if not configured. Configured via info attribute or URL. |
| socketTimeout                           | Timeout for the client to wait for server return data during TCP communication after obtaining the connection, Unit: seconds. Default is 0 seconds, waiting indefinitely. Configured via info attribute or URL. |
| loginTimeout                            | Timeout for the client to wait for server return data during login authentication after creating the socket, Unit: seconds. Default is 300 seconds if not configured. Configured via info attribute or URL. |
| SQL statement execution timeout         | Configured via the statement interface setQueryTimeout. Default has no timeout, waiting indefinitely. |

Examples of connectTimeout, socketTimeout, and loginTimeout when configured via URL are as follows:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?connectTimeout=60&socketTimeout=120&loginTimeout=60
```

#### Connection Mode Configuration

The YashanDB JDBC driver controls the connection mode and the database product name through the following parameters:

|Parameter |Description |
| --------- | ---------------------- |
| erverMode           | Specify the connection mode. The available values are as follows:<br />\* shared: The default value, indicating the connection to shared server mode, allowing client connections to use a shared thread pool (actual use of a shared thread pool depends on relevant YashanDB server configuration parameter [MAX_REACTOR_CHANNELS](../../../../参考手册/配置参数.html#max_reactor_channels)).<br />\* dedicated: Indicates the connection to the dedicated server mode, meaning each client connection request must allocate a separate thread (not affected by relevant YashanDB server configurations). |
| productName | (Hidden parameter) Specify the identifier for the database product name. When omitted, it is defaulted to be recognized as YashanDB. If specified, it can bypass the automatic recognition and take effect according to the specified value. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?connecTimeout=60&socketTimeout=120&loginTimeout=60&serverMode=dedicated
```

####  Automatic Transaction CommitConfiguration

When connecting to the YashanDB database via the JDBC driver, the client driver defaults to enabling automatic transaction commit (autoCommit set to true). However, the actual behavior varies depending on the syntax mode:

- YashanDB in yashan mode: Whether transactions are automatically committed depends on the client-side configuration.

- YashanDB in mysql mode: Whether transactions are automatically committed depends on the server-side configuration, controlled by the [system variable AUTOCOMMIT](../../../../mysql模式参考手册/系统变量/系统变量参考). If the server is not configured to auto-commit but the client is, the final behavior will still be non-auto-commit. To avoid discrepancies between actual behavior and expectations or partial configurations, it is recommended that the auto-commit settings on both the server and client sides be kept consistent.

To configure the auto-commit property at the client level, you can either specify the appropriate parameter during connection or call the [setAutoCommit](../../JDBC接口支持说明/java.sql.Connection) interface after establishing the connection.

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |----------------------------------------------|
| autoCommit| string | Whether to enable automatic transaction commit. TRUE means enabled; FALSE means disabled. The default is TRUE. |

> **Note**:
>
> If `autoCommit=false` is manually specified during connection to disable client-side automatic transaction commit, and later the client re-enables auto-commit by calling the `setAutoCommit` interface, it will trigger an automatic commit (regardless of the server-side configuration, the transaction will be automatically committed at this scenario).

Example of disabling the client's transaction auto-commit configuration via connection parameters:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?autoCommit=false
```

Example of disabling the client's transaction auto-commit configuration by calling the `setAutoCommit` interface after connection:

```java
public static Connection getConnection(String username, String passwd) {
        //Driver class
        String driver = "com.yashandb.jdbc.Driver";
        //Database connection descriptor
        String sourceURL = "jdbc:yasdb://10.10.10.2:1688/yasdb";
        Connection conn = null;

        try {
            //Load driver
            Class.forName(driver);
             //Create connection
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            // Call the setAutoCommit interface to enable or disable automatic commit
            conn.setAutoCommit(false);
            // Execute SQL statements
            Statement statement = conn.createStatement();
            statement.execute("create table area_jdbc(area_no CHAR(2) NOT NULL PRIMARY KEY,area_name VARCHAR2(60), DHQ VARCHAR2(20))");
            statement.executeUpdate("INSERT INTO area_jdbc VALUES ('01','EastChina','Shanghai')");
            // After disabling auto-commit, you must call the commit interface to explicitly commit the transaction.
            conn.commit();
            statement.close();
            conn.close();
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
```

#### Date and Time Type Conversion

Enabling date and time type conversion allows date-type columns to return timestamp-type column attributes in some interfaces. The affected interfaces are as follows:

- ResultSet.getString(int columnIndex) will return a string in Timestamp format.
- ResultSet.getObject(int columnIndex) will return a Timestamp class.
- ResultSetMetaData.getColumnClassName(int column) will return Timestamp.class.

Relevant parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |--------------------------------------------|
| mapDateToTimestamp        | string | This parameter indicates whether date-to-time conversion is enabled, TRUE means enabled, FALSE means disabled, default is FALSE. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?mapDateToTimestamp=true
```

#### Support for Multiple SQL

Enabling support for multiple SQL allows multiple SQL statements to be passed in simultaneously, separated by semicolons (`;`), with the trailing semicolon of the last SQL statement being optional. The input SQL statements will be executed in order, stopping and returning an error if an error occurs, while the JDBC interface will always only return the execution result of the first SQL statement.

Currently, only non-parameterized SQL statements are supported, and autoGeneratedKey is not allowed during execution.

The relevant interfaces that support multiple SQL are:

- Statement.executeUpdate(String sql)
- Statement.executeQuery(String sql)
- Statement.addBatch(String sql)
- Statement.execute(String sql)

Relevant parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |----------------------------------------------|
| allowMultiStmt| string | Whether to enable support for multiple SQL, TRUE means enabled, FALSE means disabled, default is FALSE. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?allowMultiStmt=true
```