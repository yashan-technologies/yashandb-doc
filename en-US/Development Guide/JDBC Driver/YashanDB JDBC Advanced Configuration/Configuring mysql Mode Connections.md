When configuring connections, YashanDB JDBC provides syntax compatibility mode settings. When the database server is in mysql mode, the corresponding connection should be configured as mysql mode to enable the driver to perform the following operations in MySQL compatibility mode:

- The username passed in will be case-sensitive

- The return value of the DatabaseMetaData.getDatabaseProductName interface will change to mysql.

When the database server is in yashan mode, no compatibility mode configuration is required, or it can be configured as yashan. 

The YashanDB JDBC driver supports some common parameters in the URL as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------ |-------------------------------|
| compatVector | string | Compatibility mode setting, only be set to mysql or yashan. |
| connectTimeout | string | Timeout for creating socket connection when establishing connection, unit is seconds. Default is 10s if not configured. |
| socketTimeout | string | Timeout for client to wait for server return data during TCP communication after obtaining connection, unit is seconds. Default is 0s, waiting indefinitely. |
| loginTimeout | string | Timeout for client to wait for server return data during login authentication after creating socket, unit is seconds. Default is 300s if not configured. |
| serverMode | string | Specify connection mode. Available values are shared (shared server mode, default) and dedicated (dedicated server mode). |
| autoCommit | string | Whether to enable automatic transaction commit. TRUE means enabled; FALSE means disabled. Default is TRUE. In mysql mode, whether transactions are auto-committed is also controlled by the database server system variable AUTOCOMMIT. |
| mapDateToTimestamp | string | This parameter indicates whether date-to-timestamp conversion is enabled. TRUE means enabled; FALSE means disabled. Default is FALSE. |
| allowMultiStmt | string | Whether to enable support for multiple SQL. TRUE means enabled; FALSE means disabled. Default is FALSE. When enabled, multiple SQL statements can be passed in simultaneously, separated by semicolons. |

URL connection configuration example:

```java
// URL connection configuration
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql

//URL multi-parameter configuration example:
// Configure mysql mode connection, and set connection timeout and socket timeout
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql&connectTimeout=60&socketTimeout=120
```

> **Note**:
>
>  In mysql mode, the transaction auto-commit behavior is determined by both the client parameter autoCommit and the database server system variable AUTOCOMMIT. If the server is not configured to auto-commit but the client is, the final behavior will still be non-auto-commit. It is recommended to keep the auto-commit configuration consistent between the server and client.

