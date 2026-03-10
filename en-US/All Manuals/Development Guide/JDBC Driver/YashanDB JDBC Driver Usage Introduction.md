## Connect to Database

### Basic Connection Configuration

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
| url       | Database connection descriptor, formatted as jdbc:yasdb://host:port/database_name<br>\*   host: Server domain name or IP address, should be configured as the address of the standalone/cluster instance server or distributed CN server. If not filled, it indicates connection to localhost.<br>\*   port: Database service port, e.g., 1688.<br>\*   database_name: Database name, mandatory. This parameter is for compatibility only, has no actual meaning, and does not validate the parameter value. |
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

#### MySQL Compatibility

jdbc provides a MySQL compatibility mode parameter, allowing the syntax mode to be set to mysql or yashan.

Relevant parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| compatVector     | string | Compatibility mode setting, needs to be set only when the server is in mysql mode, and can only be set to mysql or yashan.<br/>When set to mysql, the driver will automatically set the syntax mode to MySQL compatibility mode, and the username passed in will be case-sensitive. The return value of the DatabaseMetaData.getDatabaseProductName interface will change to mysql.<br/>When set to yashan, the driver will set the syntax mode to yashan. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?compatVector=mysql
```

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
| erverMode           | Specify the connection mode. The available values are as follows:<br />\* shared: The default value, indicating the connection to shared server mode, allowing client connections to use a shared thread pool (actual use of a shared thread pool depends on relevant YashanDB server configuration parameter [MAX_REACTOR_CHANNELS](../../参考手册/配置参数.html#max_reactor_channels)).<br />\* dedicated: Indicates the connection to the dedicated server mode, meaning each client connection request must allocate a separate thread (not affected by relevant YashanDB server configurations). |
| productName | (Hidden parameter) Specify the identifier for the database product name. When omitted, it is defaulted to be recognized as YashanDB. If specified, it can bypass the automatic recognition and take effect according to the specified value. |


URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?connecTimeout=60&socketTimeout=120&loginTimeout=60&serverMode=dedicated
```

<span id="IPs" name="IPs" class="yaslink"></span>

#### Multi-IP/PORT Connection Configuration

In high availability primary/standby and load balancing scenarios, multiple IP/PORT or multiple sets of IP/PORT can be configured for connection.

Multiple IP/PORT should be separated by commas, and connections will be made to the corresponding nodes based on the serverType configuration.

Multiple sets of IP/PORT should be separated by semicolons; connections are first made based on serverType configuration for the corresponding nodes within each group, and if all connections within the group fail, the next group will be accessed sequentially based on priority (the earlier in the order, the higher the priority).

URL parameter format:

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename

// When serverType=primary|standby, poolTimeout can be specified
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename?poolTimeout=value
```

Introduction to configuration items is as follows:

- serverType: Optional parameter, service type, available options are [primary|standby|loadBalance|primaryLoadBalance|standbyLoadBalance]. If serverType is not specified, primary is used as default when entering multiple IPs.

    - primary: Indicates priority connection to the main node, JDBC will automatically identify the main node and connect.
  
    - standby: Indicates priority connection to the backup node, JDBC will automatically identify the backup node and connect.
  
    - loadBalance: Indicates load balancing connection, JDBC will automatically identify the node with the least number of connections and connect.
  
    - primaryLoadBalance: Indicates load balancing connection to the main node, JDBC will automatically identify the main node with the least number of connections and connect. If the number of connections is the same for main nodes, the one listed first in the URL connection configuration will be prioritized.

    - standbyLoadBalance: Indicates load balancing connection to the backup node, JDBC will automatically identify the backup node with the least number of connections and connect. If the number of connections is the same for backup nodes, the one listed first in the URL connection configuration will be prioritized.

- poolTimeout: Optional parameter, connection timeout in seconds when using primary or standby service types. The default value is 300 if poolTimeout is not specified.

URL connection configuration examples:

```java
// Example 1: primary + multiple IP/PORT + poolTimeout
jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan?poolTimeout=180

// Example 2: standby + multiple sets of IP/PORT + poolTimeout
jdbc:yasdb:standby://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan?poolTimeout=180

// Example 3: loadBalance + multiple IP/PORT 
jdbc:yasdb:loadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan

// Example 4: primaryLoadBalance + multiple sets of IP/PORT
jdbc:yasdb:primaryLoadBalance://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan

// Example 5: standbyLoadBalance + multiple IP/PORT
jdbc:yasdb:standbyLoadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan
```

#### Secure Connection Configuration

The JDBC secure connection configuration includes SSL encrypted communication, TLCP encrypted communication, SM3 password hash algorithm, SM4 password encryption algorithm, and UKEY secure login. These security configurations' switches are on the server side. If the server has enabled the corresponding security configuration switch, the JDBC needs to be set accordingly; otherwise, it will be unable to obtain a connection for database operations.

##### SSL Encrypted Communication

If the server has enabled SSL encrypted communication, the JDBC must configure the following parameters:

|Configuration Parameter Name |Parameter Description |
|-------------------| ---------------------- |
| sslRootCer                   | Standard path to the root certificate for SSL encrypted communication, can be an absolute or relative path. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?sslRootCer=./ca.crt
```

##### TLCP Encrypted Communication

If the server has enabled TLCP encrypted communication, the corresponding dynamic library must be obtained from the installation package and placed in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| yashandb-gmssl-jni library | * Windows: yas_gmssl_jni.dll<br/>* Linux: libyas_gmssl_jni.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

In addition to the dynamic library, the JDBC must configure the following parameters:

|Configuration Parameter Name |Parameter Description |
|-------------------| ---------------------- |
| tlcpCacertFile               | Standard path to the root certificate for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpCertFile                 | Standard path to the client certificate for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpClientKeyFile            | Standard path to the client key file for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpClientKeyPass            | Password for the client key for TLCP encrypted communication. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?sslRootCer=D:\gmssl_ca\rootcacert.pem&tlcpCertFile=D:\gmssl_ca\clientcert.pem&tlcpClientKeyFile=D:\gmssl_ca\clientkey.pem&tlcpClientKeyPass=123456
```

##### SM3 Password Hash Algorithm

If the server has enabled the SM3 password hash algorithm, the corresponding dynamic library must be obtained from the installation package and placed in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

##### SM4 Password Encryption Algorithm

If the server has enabled the SM4 password encryption algorithm, the corresponding dynamic library must be obtained from the installation package and placed in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

<span id="UKEY" name="UKEY" class="yaslink"></span>

##### UKEY Secure Login
If the server has enabled UKEY secure login, the corresponding dynamic library must be obtained from the installation package and placed in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| libgm3000 dynamic library | * Windows: mtoken_gm3000.dll<br/>* Linux: libgm3000.1.0.so | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

In addition to the dynamic library, the JDBC must configure the following parameters:

|Parameter Name |Valid Values |Parameter Description |
|--------------|---------------------|------------------------------|
| UKeyName       | * DBA: If the user's role is DBA, the UKEY device name must be DBA.<br/>* SECURITY_ADMIN: If the user's role is Security Administrator, the UKEY device name must be SECURITY_ADMIN.<br/>* AUDIT_ADMIN: If the user's role is Auditor, the UKEY device name must be AUDIT_ADMIN. | This parameter indicates the UKEY device name to be authenticated, which must correspond to the user's role name. |
| UKeyPin        | Password value less than 64 bits | This parameter specifies the user password for the UKEY. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?UKeyName=DBA&UKeyPin=Yashan123
```

#### Transparent Application Failover (TAF) Configuration

For TAF-related configurations and usage, please refer to [Transparent Application Failover](JDBC Advanced Features/Transparent Application Failover).

#### Heartbeat Connection Configuration

Enabling heartbeat connections allows the program to detect network anomalies faster on active connections, triggering subsequent operations such as fault recovery.

Heartbeat-related configuration parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |---------------------------------------------------|
| heartbeatSwitch             | string | This parameter indicates whether heartbeat connections are enabled, with ON and OFF as options, default is ON. |
| heartbeatSocketTimeout      | int    | Heartbeat connection keep-alive timeout (in seconds), default is 60 seconds. |
| heartbeatSchedulePeriod     | int    | Heartbeat connection keep-alive interval (in seconds), default is 20 seconds. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?heartbeatSwitch=on&heartbeatSocketTimeout=60&heartbeatSchedulePeriod=20
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

## Execute SQL

### Execute Ordinary SQL

JDBC applications manipulate database data by executing SQL statements (without passing parameters) following these steps:

1. Call the createStatement method of the Connection to create a statement object.

    ```java
    Statement stmt = con.createStatement();
    ```

2. Call the execute method of the Statement to execute the SQL statement.

    ```java
    stmt.execute("CREATE TABLE table_example(id INTEGER, name VARCHAR(32))");
    ```

3. Close the statement object.

    ```java
    stmt.close();
    ```

### Execute SQL with Bound Parameters

JDBC applications manipulate database data by executing SQL statements (with bound parameters) following these steps:

1. Call the prepareStatement method of the Connection to create a statement object.

    ```java
    PreparedStatement pstmt = conn.prepareStatement("insert into tb1(col1) values(?)");
    ```

2. Call the set method to pass parameters.

    ```java
    pstmt.setObject(1, 1);
    ```

3. Call the execute method of the PreparedStatement to execute the SQL statement.

    ```java
    pstmt.execute();
    ```

4. Close the statement object.

    ```java
    pstmt.close();
    ```

> **Note**:
> 
> When passing strings using the pstmt.setString interface, note that since YashanDB introduced the [variable spying](../PL Reference Manual/Parameters/Binding Parameters) functionality in version 23.1 and later, the logic of type inference for parameters to be bound in the statement has changed. If executing the following use case in version 23.1 and later, the WHERE condition will not match successfully, while it can match successfully in versions prior to 23.1, leading to different query results.

```java
  Statement stmt = conn.createStatement();
  stmt.execute("CREATE TABLE T1(id INT, data CHAR(10))");
  stmt.execute("INSERT INTO T1 VALUES(1, 'abc')");
  stmt.execute("COMMIT");
  
  PreparedStatement pstmt = conn.prepareStatement("select id from t1 where data=?");
  pstmt.setString(1, "abc");
  pstmt.execute();
  // Process the query results
```

For bound parameter execution scenarios, the JDBC client will first send a prepare command to the server, then send the bound parameter execution. You can enable the support for direct execution of bound parameters, allowing the SQL statement of prepareStatement and the parameters to be bound to be sent to the server at once, reducing interaction frequency. The enabling methods are:

|Configuration/Interface |Description |Example |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clientPrepare configuration parameter             | To enable direct execution with bound parameters<br/>* true: means enabled<br/>* false: means disabled | jdbc:yasdb://192.168.1.2:1688/yashan?clientPrepare=true |
| void setClientPrepare(boolean clientPrepare)    | Extension method of the YasConnection interface | Connection connection = DriverManager.getConnection(url); ((YasConnection)connection).setClientPrepare(true); |

***Example***: 

```java
  Connection conn = getConnection("sales", "sales");
  ((YasConnection)conn).setClientPrepare(true);
  PreparedStatement preparedStatement = conn.prepareStatement("select ? from dual");

  Clob clob = conn.createClob();
  String testLobString = "test clob direct bind";
  clob.setString(1, testLobString);
  preparedStatement.setClob(1, clob);
  preparedStatement.execute();

  ResultSet resultSet = preparedStatement.getResultSet();
  resultSet.next();
  Clob clob1 = resultSet.getClob(1);
  clob.free();
  resultSet.close();

  preparedStatement.setInt(1,1);
  preparedStatement.execute();
  
  resultSet = preparedStatement.getResultSet();
  resultSet.next();
  int iValue1 = resultSet.getInt(1);
  resultSet.close();

  preparedStatement.close();
  conn.close();
```

## Get Result Set

### Set Result Set Type

Different types of result sets have their respective application scenarios, and applications need to select the appropriate result set type based on actual circumstances. In the process of executing SQL statements, it is required to first create the corresponding statement object. Some methods for creating statement objects provide functionality to set the result set type. The relevant Connection methods are as follows:

```java
//Create a Statement object.
createStatement();
 
//Create a PreparedStatement object.
prepareStatement(String sql);
 
//Create a CallableStatement object.
prepareCall(String sql);
```

The result set type descriptions are as follows:

|Parameter |Description |
| -------------------- | ------------------------------------------------------------ |
| resultSetType    | Indicates the type of the result set. Specifically, there are three types:<br>\*   ResultSet.TYPE_FORWARD_ONLY: ResultSet can only move forward, is the default value.<br>\*   ResultSet.TYPE_SCROLL_SENSITIVE: Can scroll back to the modified row after modification to see the modified result.<br>\*   ResultSet.TYPE_SCROLL_INSENSITIVE: Edits made to editable routines are not displayed. |
| resultSetConcurrency | Indicates the concurrency of the result set, specifically two types:<br>\*   ResultSet.CONCUR_READ_ONLY: Cannot update data in the result set unless establishing a new update statement from data in the result set.<br>\*   ResultSet.CONCUR_UPDATEABLE: Editable result set. For scrollable result sets, appropriate changes can be made to the result set. |

### Positioning in Result Set

The ResultSet object has a cursor pointing to its current data row. Initially, the cursor is positioned before the first row. The next method moves the cursor to the next row; since this method returns false when there are no next rows in the ResultSet object, it can be used in a while loop to iterate through the result set. However, for scrollable result sets, the JDBC driver program provides more positioning methods, as follows:

#### Methods for Positioning in Result Set

|Method |Description |
| ------------- | --------------------------------- |
| next()         | Moves the ResultSet down by one row. |
| previous()     | Moves the ResultSet up by one row. |
| beforeFirst()  | Positions the ResultSet before the first row. |
| afterLast()    | Positions the ResultSet after the last row. |
| first()        | Positions the ResultSet to the first row. |
| last()         | Positions the ResultSet to the last row. |
| absolute(int)  | Moves the ResultSet to the row specified by the parameter. |
| relative(int)  | Moves forward or backward by the number of rows specified by the parameter. |

#### Get Result Set Cursor Position

|Method |Description |
| --------------- | -------------------- |
| isFirst()      | Checks if it is on a row. |
| isLast()       | Checks if it is on the last row. |
| isBeforeFirst()| Checks if it is before the first row. |
| isAfterLast()  | Checks if it is after the last row. |
| getRow()       | Gets the current row number. |

#### Get Data in Result Set

|Method |Description |
| ------------------------------------------------------ | ----------------------------- |
| boolean getBoolean(int columnIndex)                      | Retrieves boolean data by column index. |
| boolean getBoolean(String columnLabel)                   | Retrieves boolean data by column name. |
| byte getByte(int columnIndex)                            | Retrieves byte data by column index. |
| byte getByte(String columnLabel)                         | Retrieves byte data by column name. |
| short getShort(int columnIndex)                          | Retrieves short data by column index. |
| short getShort(String columnLabel)                       | Retrieves short data by column name. |
| long getLong(int columnIndex)                            | Retrieves long data by column index. |
| long getLong(String columnLabel)                         | Retrieves long data by column name. |
| float getFloat(int columnIndex)                          | Retrieves float data by column index. |
| float getFloat(String columnLabel)                       | Retrieves float data by column name. |
| double getDouble(int columnIndex)                        | Retrieves double data by column index. |
| double getDouble(String columnLabel)                     | Retrieves double data by column name. |
| BigDecimal getBigDecimal(int columnIndex)                | Retrieves BigDecimal data by column index. |
| BigDecimal getBigDecimal(String columnLabel)             | Retrieves BigDecimal data by column name. |
| byte[] getBytes(int columnIndex)                         | Retrieves byte[] data by column index. |
| byte[] getBytes(String columnLabel)                      | Retrieves byte[] data by column name. |
| int getInt(int columnIndex)                              | Retrieves int data by column index. |
| int getInt(String columnLabel)                           | Retrieves int data by column name. |
| RowId getRowId(int columnIndex)                          | Retrieves RowId data by column index. |
| RowId getRowId(String columnLabel)                       | Retrieves RowId data by column name. |
| String getString(int columnIndex)                        | Retrieves String data by column index. |
| String getString(String columnLabel)                     | Retrieves String data by column name. |
| Time getTime(int columnIndex) throws SQLException;       | Retrieves Time data by column index. |
| Time getTime(String columnLabel) throws SQLException;     | Retrieves Time data by column name. |
| Timestamp getTimestamp(int columnIndex)                  | Retrieves Timestamp data by column index. |
| Timestamp getTimestamp(String columnLabel)               | Retrieves Timestamp data by column name. |
| Date getDate(int columnIndex)                            | Retrieves Date data by column index. |
| Date getDate(String columnLabel)                         | Retrieves Date data by column name. |

#### **Get Streaming Result Set**

The streaming result set is enabled when stmt is set with fetchSize to Integer.MIN_VALUE, result set type is ResultSet.TYPE_FORWARD_ONLY, and resultSetConcurrency is ResultSet.CONCUR_READ_ONLY.

***Example***: 
```java
Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);
stmt.setFetchSize(Integer.MIN_VALUE);
```

You can also configure the connection through URL; when configured, the created statement under this connection will perform streaming fetch by default, example:
```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?defaultRowFetchSize=-2147483648&netTimeoutForStreamingResults=120";

Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement();
```

|Parameter |Description |
| ----------------------------- | ------------------------------------------ |
| defaultRowFetchSize                 | Number of rows fetched at a time during statement execution |
| netTimeoutForStreamingResults       | Blocking timeout for the server's sending after starting streaming results |

## JDBC Driver Logging Configuration and Printing

The YashanDB JDBC driver records logs at runtime, divided into five levels: error, warn, info, debug, and trace.

The logging functionality of the YashanDB JDBC driver supports two facade technologies: SLF4J and JCL (Apache Commons Logging), and all current logging frameworks such as log4j, log4j2, logback, and JUL (Java's native java.util.logging) are supported.

To quickly identify performance issues occurring in business, the YashanDB JDBC driver adds log information at the start and end of operations such as obtaining connections, closing connections, SQL pre-compilation, binding execution, direct execution, and fetching results; these logs are at trace level.

Under normal circumstances, users do not need to perform additional log configuration; the system will print the JDBC driver's logs along with the application's business logs according to the logging level configured by the application.

### Log Content

**Obtain Connection**

Start: `connect start, timestamp:{}, ip/port:{}, serverType:{}`.

End: `connect end, timestamp:{}, session id:{}, connectVersion:{}`.

**Close Connection**

Start: `connect close start, timestamp:{}, session id:{}`.

End: `connect close end, timestamp:{}, session id:{}`.

**Pre-compile**

Start: `prepare start, timestamp:{}, session id:{}, sql:{}`.

End: `prepare end, timestamp:{}, session id:{}, statement id:{}`.

**Bind Execute**

Start: `prepareStatement execute start, timestamp:{}, session id:{}, Statement id:{}`.

End: `prepareStatement execute end, timestamp:{}, session id:{}, Statement id:{}`.

**Direct Execute**

Start: `statement execute start, timestamp:{}, Statement id:{}, sql:{}`.

End: `statement execute end, timestamp:{}, session id:{}, Statement id:{}`.

**fetch**

Start: `fetchMore start, timestamp:{}, session id:{}, Statement id:{}`.

End: `fetchMore end, timestamp:{}, session id:{}, Statement id:{}`.

### Log Level and Path Independent Configuration

The interaction information's log level is trace, and to print such logs, one only needs to adjust the application's log level to trace. However, this configuration will cause all trace-level logs of the application to be printed, which may lead to excessive log outputs.

To avoid printing excessive irrelevant log information, it is advisable to independently configure the log level and path for the driver package (com.yashandb), setting the log level of the com.yashandb package to trace or separating driver logs and business logs into different paths.

Mainstream frameworks such as log4j, log4j2, logback, and JUL support setting log levels and paths by package. The following example shows the logging-config.xml configuration for Spring Boot's default logback logging framework. Similar configurations apply to other logging frameworks.

> **Note**:
>
> In the native JDK's JUL logging framework, log levels range from high to low as SEVERE, WARNING, INFO, CONFIG, FINE, FINER, FINEST, etc. The trace level in other logging frameworks corresponds to the FINEST level in JUL.

```xml
<configuration>
    <contextName>jdbc-mybatis</contextName>
    <property name = "dir" value = "log"/>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <!--Display format layout-->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
            </pattern>
        </layout>

    </appender>

    <appender name="fileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/project_log.log</File>
        <!--Rolling policy, classified by time-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--File path, defines log slicing method—archiving each day's logs to a file to prevent logs from filling up the entire disk space-->
            <FileNamePattern>${dir}/project_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--Only keep logs for the last 90 days-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--Log output encoding format-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="fileLog1" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/driver_log.log</File>
        <!--Rolling policy, classified by time-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--File path, defines log slicing method—archiving each day's logs to a file to prevent logs from filling up the entire disk space-->
            <FileNamePattern>${dir}/driver_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--Only keep logs for the last 90 days-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--Log output encoding format-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- Set the log level for the driver independently to TRACE -->
    <logger name="com.yashandb" level="TRACE" additivity="false">
        <!-- Configure the driver log appender to fileLog1 defined above, i.e., ${dir}/driver_log.log -->
        <appender-ref ref="fileLog1"/>
    </logger>

    <!-- Set the log level of the DAO layer to DEBUG -->
    <logger name="com.example.dao" level="DEBUG" additivity="false">
        <appender-ref ref="fileLog"/>
    </logger>
    
    <root level="INFO">
        <!--Appenders will be added to this logger-->
        <appender-ref ref="consoleLog1"/>
        <appender-ref ref="fileLog"/>
    </root>

</configuration>
```
