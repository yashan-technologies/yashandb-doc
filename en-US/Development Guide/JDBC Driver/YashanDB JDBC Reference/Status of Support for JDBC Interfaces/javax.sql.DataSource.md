## Description

`javax.sql.DataSource` interface supports users to establish database connections in different places after configuring the DataSource properties once.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/DataSource.html).

## Method

The YashanDB JDBC driver has the following methods for the DataSource interface:

|Method |Return Type |
| --- | --- |
| getConnection() | Connection |
| getConnection(String username, String password) | Connection |
| setUser(String userID) | void |
| getUser() | String |
| setPassword(String password) | void |
| setPortNumber(int port) | void |
| getPortNumber() | String |
| setServerName(String sn) | void |
| getServerName() | String |
| setURL(String url) | void |
| getURL() | String |
| setDatabaseName(String name)  | void   |
| getDatabaseName()  | String  |
| getReference() | Reference |
| setLoginTimeout(int seconds) | void |
| getLoginTimeout() | int |
| setSslRootCer(String sslRootCer) | void |
| getSslRootCer() | String |
| setDescription(String value) | void |
| getDescription() | String |
| createShardingKeyBuilder() | ShardingKeyBuilder |
| createConnectionBuilder() | ConnectionBuilder |

## Example

```java
package quickstart;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

public class DataSourceExample {

    // YashanDB DataSource configuration
    public DataSource createYashanDBDataSource() throws SQLException {
        com.yashandb.jdbc.YasDataSource ds = new com.yashandb.jdbc.YasDataSource();

        ds.setServerName("172.16.91.111");
        ds.setPortNumber(1688);
        ds.setDatabaseName("yasdb");
        ds.setUser("sales");
        ds.setPassword("sales");

        // Connection properties
        ds.setLoginTimeout(30);

        return ds;
    }

    // Use DataSource to get connection
    public void useDataSource(DataSource ds) throws SQLException {
        try (Connection conn = ds.getConnection()) {
            System.out.println("Connected to: " + conn.getMetaData().getDatabaseProductName());
        }
    }
}
```

