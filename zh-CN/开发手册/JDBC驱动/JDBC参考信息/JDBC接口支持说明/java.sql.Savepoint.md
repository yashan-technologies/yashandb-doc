## 描述

`java.sql.Savepoint`用来表示保存点对象，Savepoint可以是命名的，也可以是未命名的。实例通过`Connection.setSavepoint()`方法创建，仅在事务未提交或未回滚时有效。核心作用是：


- 在一个活跃的数据库事务中创建“保存点”，将一个大事务拆分为多个可独立回滚的小片段。
- 支持回滚到指定保存点（仅撤销保存点之后的操作，保存点之前的操作仍保留）。
- 可手动释放不再需要的保存点，节省数据库资源。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Savepoint.html)。

## 方法

YashanDB JDBC驱动对Savepoint接口有如下方法：

| 方法|  返回类型| 备注|
|--------------------|----------------------|-----------------------|
| getSavepointId()   | int                  | 只有未命名的保存点可以调用，否则会抛异常。 |
| getSavepointName() | String               | 只有命名的保存点可以调用，否则会抛异常。  |

## 示例

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Savepoint;
import java.util.Properties;

public class SavepointExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // 条件回滚到保存点
    public void conditionalRollback(Connection conn) throws SQLException {
        conn.setAutoCommit(false);

        Savepoint checkpoint1 = conn.setSavepoint("CHECKPOINT_1");
        System.out.println("Checkpoint 1: " + checkpoint1.getSavepointName());

        // 执行若干业务操作
        boolean success = performOperation(conn);

        if (!success) {
            // 回滚至指定保存点
            conn.rollback(checkpoint1);
            System.out.println("Operation failed, rolled back to: " +
                    checkpoint1.getSavepointName());
        } else {
            Savepoint checkpoint2 = conn.setSavepoint("CHECKPOINT_2");
            System.out.println("Checkpoint 2 ID: " + checkpoint2.getSavepointId());

            // 继续后续执行逻辑……
        }

        conn.commit();
        conn.setAutoCommit(true);
    }

    private boolean performOperation(Connection conn) throws SQLException {
        // 执行业务操作
        return true;
    }
}
```

