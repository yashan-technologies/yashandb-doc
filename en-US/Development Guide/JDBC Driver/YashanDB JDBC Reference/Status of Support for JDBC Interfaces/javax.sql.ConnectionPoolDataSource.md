## Description

`javax.sql.ConnectionPoolDataSource` is the dedicated connection pool data source interface defined in the JDBC extension package. Its core positioning is:

- Provides reusable physical database connections (`PooledConnection`) for the database connection pool;
- Serves as the underlying dependency for connection pool frameworks, and is not directly used by ordinary applications but called by connection pool implementation classes;
- The core difference from the ordinary `DataSource`: `DataSource` returns a directly usable `Connection`, while `ConnectionPoolDataSource` returns a `PooledConnection` (the connection object managed by the connection pool).

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/ConnectionPoolDataSource.html).

## Method

YashanDB has the following methods for the ConnectionPoolDataSource interface:

|Method |Return Type |
| --- | --- |
| getPooledConnection() | PooledConnection |
| getPooledConnection​(String user, String password) | PooledConnection |

## Example

```java
package quickstart;

import javax.sql.ConnectionPoolDataSource;
import javax.sql.PooledConnection;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class SimpleConnectionPool {
    private ConnectionPoolDataSource cpds;
    private List<PooledConnection> pool = new ArrayList<>();
    private final int POOL_SIZE = 10;

    public SimpleConnectionPool(ConnectionPoolDataSource cpds) {
        this.cpds = cpds;
        initializePool();
    }

    private void initializePool() throws SQLException {
        for (int i = 0; i < POOL_SIZE; i++) {
            PooledConnection pooledConn = cpds.getPooledConnection();
            pool.add(pooledConn);
        }
    }

    public Connection getConnection() throws SQLException {
        if (pool.isEmpty()) {
            throw new SQLException("No available connections");
        }

        PooledConnection pooledConn = pool.remove(0);
        return pooledConn.getConnection();
    }

    public void returnConnection(Connection conn) throws SQLException {
        // In actual implementation, we need to find the corresponding PooledConnection
        // Simplified handling here
        PooledConnection pooledConn = cpds.getPooledConnection();
        pool.add(pooledConn);
    }

    public void closeAll() throws SQLException {
        for (PooledConnection pc : pool) {
            pc.close();
        }
        pool.clear();
    }
}
```

