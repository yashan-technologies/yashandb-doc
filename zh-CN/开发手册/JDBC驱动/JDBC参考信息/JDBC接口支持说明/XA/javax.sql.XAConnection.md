## 描述

XAConnection接口用于获取XAResource对象。

`javax.sql.XAConnection`是JDBC规范中用于支持分布式事务（XA事务）的核心接口，它继承自`java.sql.Connection`的父接口，`XAConnection`能关联到XA事务管理器，对数据库执行提交回滚操作，保证分布式场景下的数据一致性。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/XAConnection.html)。

## 方法

YashanDB JDBC驱动对XAConnection接口有如下方法：

| 方法|  返回类型|
|-------------------|----------------------------------|
| getXAResource()   | javax.transaction.xa.XAResource  |

> **Note**: 
>
> XAConnection接口是PooledConnection接口的子接口，所以PooledConnection中的接口都可以用。

## 示例

```java
package quickstart;

// JdbcDebugExample.java
// 演示基于JDBC调试存储过程的主要步骤

import com.yashandb.jdbc.DebugBreakpoint;
import com.yashandb.jdbc.YasConnection;
import com.yashandb.jdbc.YasDebugCallableStatement;
import com.yashandb.jdbc.YasTypes;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.List;

public class JdbcDebugExample {

    // 创建数据库连接。
    public static Connection getConnection() {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        Connection conn = null;
        try {
            // 加载数据库驱动。
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        try {
            // 创建数据库连接。
            conn = DriverManager.getConnection(sourceURL, user, password);
            System.out.println("Connection succeed!");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }
    

    // 调试存储过程
    public static void debugMode(Connection conn) throws SQLException {
        Statement statement = null;
        YasDebugCallableStatement debugStatement = null;
        ResultSet resultSet = null;
        ResultSet resultSet2 = null;
        try {
            statement = conn.createStatement();
            // 创建调试的存储过程
            statement.execute("create or replace procedure test_debug_add(p1 in int, p2 in int, p3 out int) is\n"
                    + "begin\n"
                    + "    p3 := p1 + p2;\n"
                    + "    p3 := p3 + p2;\n"
                    + "end;");
            // 获取存储过程的OBJECT_ID,SUBPROGRAM_ID
            resultSet = statement.executeQuery("select OBJECT_ID,SUBPROGRAM_ID from dba_procedures "
                    + "where OBJECT_NAME = 'TEST_DEBUG_ADD'");
            if (resultSet.next()) {
                long objectId = resultSet.getLong(1);
                int subprogramId = resultSet.getInt(2);
                // 获取存储过程的VERSION
                resultSet2 = statement.executeQuery("select VERSION from ALL_SOURCE where NAME = 'TEST_DEBUG_ADD'");
                if (resultSet2.next()) {
                    int version = resultSet2.getInt(1);
                    // 创建调试statement
                    debugStatement = ((YasConnection) conn).createDebugStatement(
                            "begin test_debug_add(?, ?, ?); end;", objectId, subprogramId, version);
                    // 设置参数和出参
                    debugStatement.clearParameters();
                    debugStatement.setInt(1, 1);
                    debugStatement.setInt(2, 2);
                    debugStatement.registerOutParameter(3, YasTypes.INTEGER);
                    // 添加断点
                    DebugBreakpoint debugBreakpoint = debugStatement.pdbgAddBreakpoint(objectId, subprogramId, 4);
                    // 删除断点
                    debugStatement.pdbgDeleteBreakpoint(debugBreakpoint);
                    
                    // 开始调试
                    debugStatement.pdbgStart();
                    // 添加断点
                    DebugBreakpoint debugBreakpoint2 = debugStatement.pdbgAddBreakpoint(objectId, subprogramId, 4);
                    // 展示多有断点
                    List<DebugBreakpoint> debugBreakpoints = debugStatement.pdbgShowBreakpoints();
                    // 继续执行
                    debugStatement.pdbgStepInto();
                    debugStatement.pdbgStepOut();
                    debugStatement.pdbgStepNext();
                    debugStatement.pdbgContinue();
                    // 退出debug模式
                    debugStatement.pdbgAbort();
                }
            }
        } finally {
            if (resultSet != null) {
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (resultSet2 != null) {
                try {
                    resultSet2.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (statement != null) {
                try {
                    statement.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (debugStatement != null) {
                try {
                    debugStatement.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        Connection conn = getConnection();
        if (conn != null) {
            try {
                debugMode(conn);
            } catch (SQLException e) {
                e.printStackTrace();
            } finally {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

