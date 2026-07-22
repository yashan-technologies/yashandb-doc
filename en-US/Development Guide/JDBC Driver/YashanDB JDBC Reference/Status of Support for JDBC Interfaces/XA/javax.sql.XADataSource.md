## Description

The XADataSource interface represents that it is used to obtain XAConnection objects.

`javax.sql.XADataSource` represents that it is the core interface in the JDBC specification for creating database connections (XAConnection) that support distributed transactions (XA transactions). `XADataSource` is dedicated to generating `XAConnection` instances, enabling each connection to be managed and controlled by an XA transaction manager, thus ensuring the consistency of distributed transactions across multiple databases or resources.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/XADataSource.html).

## Method

The YashanDB JDBC driver has the following methods for the XADataSource interface:

|Method |Return Type |
|--------------------------------------------------------------------|----------------------|
| getXAConnection() throws SQLException;                             | XAConnection         |
| getXAConnection(String user, String password) throws SQLException; | XAConnection         |

> **Note**: 
>
> The XADataSource implementation of YashanDB is YasXADataSource. This implementation also inherits from javax.sql.DataSource, so the methods supported in javax.sql.DataSource can be called on YasXADataSource.

## Example

```java
package quickstart;

import javax.sql.XADataSource;
import javax.sql.XAConnection;
import javax.transaction.xa.XAResource;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

public class XADataSourceExample {

    // Create YashanDB XADataSource
    public XADataSource createXADataSource() throws SQLException {
        com.yashandb.xa.YasXADataSource ds = new com.yashandb.xa.YasXADataSource();

        ds.setServerName("172.16.91.111");
        ds.setPortNumber(1688);
        ds.setDatabaseName("yasdb");
        ds.setUser("sales");
        ds.setPassword("sales");

        return ds;
    }

    // Use XADataSource to get XAConnection
    public void useXADataSource(XADataSource xaDS) throws SQLException {
        // Get XA connection
        try (XAConnection xaConn = xaDS.getXAConnection()) {
            // Get XAResource
            XAResource xaRes = xaConn.getXAResource();
            System.out.println("XAResource: " + xaRes.getClass().getName());

            // Get regular connection
            try (Connection conn = xaConn.getConnection()) {
                try (Statement stmt = conn.createStatement()) {
                    stmt.execute("SELECT 1");
                }
            }
        }
    }
}
```

