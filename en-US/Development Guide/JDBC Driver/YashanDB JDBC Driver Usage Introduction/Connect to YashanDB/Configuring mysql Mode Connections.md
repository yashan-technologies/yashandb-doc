When configuring connections, YashanDB JDBC provides syntax compatibility mode settings. When the database server is in mysql mode, the corresponding connection should be configured as mysql mode to enable the driver to perform the following operations in MySQL compatibility mode:

- The username passed in will be case-sensitive

- The return value of the DatabaseMetaData.getDatabaseProductName interface will change to mysql.

When the database server is in yashan mode, no compatibility mode configuration is required, or it can be configured as yashan.

Relevant parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------ |-------------------------------|
| compatVector | string | Compatibility mode setting, only be set to mysql or yashan. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql
```
