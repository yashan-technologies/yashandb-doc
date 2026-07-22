## 描述

XADataSource接口用于获取XAConnection对象。

`javax.sql.XADataSource`是JDBC规范中用于创建支持分布式事务（XA事务）的数据库连接（XAConnection）的核心接口，`XADataSource`专门生产`XAConnection`实例，让每个连接都能被XA事务管理器管控，从而实现跨多个数据库或资源的分布式事务一致性。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/XADataSource.html)。

## 方法

YashanDB JDBC驱动对XADataSource接口有如下方法：

| 方法|  返回类型|
|--------------------------------------------------------------------|----------------------|
| getXAConnection() throws SQLException;                             | XAConnection         |
| getXAConnection(String user, String password) throws SQLException; | XAConnection         |

> **Note**: 
>
> YashanDB的XADataSource实现为YasXADataSource，该实现同时继承了javax.sql.DataSource，所以javax.sql.DataSource中支持的方法在YasXADataSource上均可调用。

## 示例

```java
package quickstart;

import javax.sql.XADataSource;
import javax.sql.XAConnection;
import javax.transaction.xa.XAResource;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

public class XADataSourceExample {

    // 创建 YashanDB XADataSource
    public XADataSource createXADataSource() throws SQLException {
        com.yashandb.xa.YasXADataSource ds = new com.yashandb.xa.YasXADataSource();

        ds.setServerName("172.16.91.111");
        ds.setPortNumber(1688);
        ds.setDatabaseName("yasdb");
        ds.setUser("sales");
        ds.setPassword("sales");

        return ds;
    }

    // 使用 XADataSource 获取 XAConnection
    public void useXADataSource(XADataSource xaDS) throws SQLException {
        // 获取 XA 连接
        try (XAConnection xaConn = xaDS.getXAConnection()) {
            // 获取 XAResource
            XAResource xaRes = xaConn.getXAResource();
            System.out.println("XAResource: " + xaRes.getClass().getName());

            // 获取普通连接
            try (Connection conn = xaConn.getConnection()) {
                try (Statement stmt = conn.createStatement()) {
                    stmt.execute("SELECT 1");
                }
            }
        }
    }
}
```

