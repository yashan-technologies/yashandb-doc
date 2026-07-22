## Description

`javax.sql.PooledConnection` represents that when users establish multiple database connections through DataSource, these connections can be placed in a Connection Pool. In this scenario, the Connection Pool can help users cache and reuse physical connections, improving application performance and scalability.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/PooledConnection.html).

## Method

YashanDB has the following methods for the PooledConnection interface:

|Method |Return Type |
| --- | --- |
| addConnectionEventListener​(ConnectionEventListener listener) | void |
| addStatementEventListener​(StatementEventListener listener) | void |
| close() | void |
| getConnection() | Connection |
| removeConnectionEventListener​(ConnectionEventListener listener) | void |
| removeStatementEventListener​(StatementEventListener listener) | void |

## Example

```java
package quickstart;

import javax.sql.ConnectionEvent;
import javax.sql.ConnectionEventListener;
import javax.sql.PooledConnection;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class ConnectionPoolMonitor {
    private List<PooledConnection> connections = new ArrayList<>();

    public void monitorPooledConnection(PooledConnection pooledConn) {
        // Add connection event listener
        pooledConn.addConnectionEventListener(new ConnectionEventListener() {
            @Override
            public void connectionClosed(ConnectionEvent event) {
                Connection conn = event.getConnection();
                System.out.println("[" + new Date() + "] Connection closed: " +
                        conn.hashCode());

                // Record connection usage statistics
                recordConnectionUsage(conn);
            }

            @Override
            public void connectionErrorOccurred(ConnectionEvent event) {
                SQLException error = event.getSQLException();
                System.out.println("[" + new Date() + "] Connection error: " +
                        error.getMessage());

                // Error detected, may need to send alert
                alertConnectionError(error);
            }
        });

        connections.add(pooledConn);
    }

    public void stopMonitoring(PooledConnection pooledConn) {
        // Remove all listeners
        // In actual implementation, we need to save listener references
        // Simplified handling here
        connections.remove(pooledConn);
    }

    private void recordConnectionUsage(Connection conn) {
        // Record connection usage statistics
    }

    private void alertConnectionError(SQLException error) {
        // Error detected, may need to send alert
    }
}
```

