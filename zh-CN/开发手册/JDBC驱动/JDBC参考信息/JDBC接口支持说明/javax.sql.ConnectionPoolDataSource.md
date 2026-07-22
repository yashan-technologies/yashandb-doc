## 描述

`javax.sql.ConnectionPoolDataSource`是JDBC扩展包中定义的连接池专用数据源接口，核心定位是：

- 为数据库连接池提供可复用的物理数据库连接（`PooledConnection`）。
- 是连接池框架的底层依赖，普通应用不会直接使用，由连接池实现类调用。
- 与普通`DataSource`的核心区别：`DataSource`返回直接可用的`Connection`，而`ConnectionPoolDataSource`返回`PooledConnection`（连接池管理的连接对象）。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/ConnectionPoolDataSource.html)。

## 方法

YashanDB对ConnectionPoolDataSource接口有如下方法：

| 方法|  返回类型|
| --- | --- |
| getPooledConnection() | PooledConnection |
| getPooledConnection​(String user, String password) | PooledConnection |

## 示例

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
        // 在实际实现中，我们需要找到对应的PooledConnection
        // 简化处理
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

