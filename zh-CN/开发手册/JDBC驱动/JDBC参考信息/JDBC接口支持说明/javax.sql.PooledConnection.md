## 描述

`javax.sql.PooledConnection`表示当用户通过DataSource建立多个数据库连接时，可将这些连接放在连接池Connection Pool中，此时Connection Pool可帮助用户实现对物理连接的缓存和重用，能提高应用的性能和可扩展性。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/PooledConnection.html)。

## 方法

YashanDB对PooledConnection接口有如下方法：

| 方法|  返回类型|
| --- | --- |
| addConnectionEventListener​(ConnectionEventListener listener) | void |
| addStatementEventListener​(StatementEventListener listener) | void |
| close() | void |
| getConnection() | Connection |
| removeConnectionEventListener​(ConnectionEventListener listener) | void |
| removeStatementEventListener​(StatementEventListener listener) | void |

## 示例

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
        // 添加连接事件监听器
        pooledConn.addConnectionEventListener(new ConnectionEventListener() {
            @Override
            public void connectionClosed(ConnectionEvent event) {
                Connection conn = event.getConnection();
                System.out.println("[" + new Date() + "] Connection closed: " +
                        conn.hashCode());

                // 记录连接使用统计
                recordConnectionUsage(conn);
            }

            @Override
            public void connectionErrorOccurred(ConnectionEvent event) {
                SQLException error = event.getSQLException();
                System.out.println("[" + new Date() + "] Connection error: " +
                        error.getMessage());

                // 检测到错误，可能需要发送告警
                alertConnectionError(error);
            }
        });

        connections.add(pooledConn);
    }

    public void stopMonitoring(PooledConnection pooledConn) {
        // 移除所有监听器
        // 在实际实现中，我们需要保存监听器引用
        // 此处简化处理
        connections.remove(pooledConn);
    }

    private void recordConnectionUsage(Connection conn) {
        // 记录连接使用统计
    }

    private void alertConnectionError(SQLException error) {
        // 检测到错误，可能需要发送告警
    }
}
```

