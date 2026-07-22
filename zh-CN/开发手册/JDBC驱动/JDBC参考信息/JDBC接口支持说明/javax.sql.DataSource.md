## 描述

`javax.sql.DataSource`接口支持用户通过一次配置DataSource的属性后，在不同地方均可建立数据库连接Connection。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/DataSource.html)。

## 方法

YashanDB JDBC驱动对DataSource接口有如下方法：

| 方法|  返回类型|
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

## 示例

```java
package quickstart;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

public class DataSourceExample {

    // YashanDB数据源配置
    public DataSource createYashanDBDataSource() throws SQLException {
        com.yashandb.jdbc.YasDataSource ds = new com.yashandb.jdbc.YasDataSource();

        ds.setServerName("172.16.91.111");
        ds.setPortNumber(1688);
        ds.setDatabaseName("yasdb");
        ds.setUser("sales");
        ds.setPassword("sales");

        // 连接属性
        ds.setLoginTimeout(30);

        return ds;
    }

    // 使用DataSource获取连接
    public void useDataSource(DataSource ds) throws SQLException {
        try (Connection conn = ds.getConnection()) {
            System.out.println("Connected to: " + conn.getMetaData().getDatabaseProductName());
        }
    }
}
```

