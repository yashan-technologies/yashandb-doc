## 描述

`YasConnection`是连接YashanDB数据库的类构造方法，作用是建立应用程序与YashanDB数据库之间的网络连接，是操作YashanDB数据库的基础入口，所有对YashanDB的查询、写入、修改等操作，都需要先通过该函数创建有效的连接对象。


## 方法

YashanDB JDBC驱动对连接YashanDB数据库功能有如下方法：

| 方法| 返回类型| 备注|
| ------------------ | ------------------------- | ------------------------------------------------------------ |
| createDebugStatement<br/>(String plsql, long objectId, int subprogramId, int version) | YasDebugCallableStatement |创建调试器会话<br/>objectId、subprogramId从dba_procedures视图查询获取<br/>version从ALL_SOURCE视图查询获取<br/>- plsql：调试存储过程的plsql<br/>-  objectId：被调试存储过程的objectId<br/>-  subprogramId：被调试存储过程的subprogramId<br/>-  version：被调试存储过程的version |
| createDebugStatement<br/>(String plsql) | YasDebugCallableStatement | 创建调试器会话<br/>plsql：调试存储过程的plsql |
| yacPdbgStart()   | void       | 立即开始调试器模式           |
| yacPdbgAddBreakpoint<br/>(long objectId, int subprogramId, int lineNum) | DebugBreakpoint     |增加断点 <br/>- objectId：断点所造存储过程的objectId<br/>- subprogramId：断点所造存储过程的subprogramId<br/>-  lineNum：断点所造存储过程的行号 |
| yacPdbgDeleteBreakpoint<br/>(DebugBreakpoint breakpoint)     | void                      | breakpoint：要被删除的断点                                   |
| pdbgShowBreakpoints()                                        | List<DebugBreakpoint>     | 展示所有断点 |
| yacPdbgDeleteAllBreakpoints()                                | void                      | 删除所有断点 |
| yacPdbgStepInto()                                            | void                      | 单步执行，如果遇到子方法，进入子方法执行 |
| yacPdbgStepOut()                                             | void                      | 执行完当前方法并退出 |
| yacPdbgContinue()                                            | void                      | 继续执行，直到直接结束或者遇到断点 |
| yacPdbgStepNext()                                            | void                      | 执行下一行，遇到子方法不会进入子方法，但是子方法会执行 |
| pdbgShowFrameVariables()                                     | List<DebugVar>            | 展示当前堆栈的所有变量信息 |
| pdbgShowFrames()                                             | List<DebugFrame>          | 展示当前所有的堆栈信息 |
| getRunObjectId()                                             | long                      | 展示当前运行存储过程的objectId |
| getRunSubprogramId()                                         | int                       | 展示当前运行存储过程的subprogramId |
| getRunLineNum()                                              | int                       | 展示当前运行存储过程的行号 |
| yacPdbgAbort()                                               | void                      | 立即退出调试器模式 |

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
        try {
            statement = conn.createStatement();
            // 创建调试的存储过程
            statement.execute("create or replace procedure test_debug_add(p1 in int, p2 in int, p3 out int) is\n"
                    + "begin\n"
                    + "    p3 := p1 + p2;\n"
                    + "    p3 := p3 + p2;\n"
                    + "end;");
            // 获取存储过程的OBJECT_ID,SUBPROGRAM_ID
            ResultSet resultSet = statement.executeQuery("select OBJECT_ID,SUBPROGRAM_ID from dba_procedures "
                    + "where OBJECT_NAME = 'TEST_DEBUG_ADD'");
            resultSet.next();
            long objectId = resultSet.getLong(1);
            int subprogramId = resultSet.getInt(2);
            // 获取存储过程的VERSION
            ResultSet resultSet2 = statement.executeQuery("select VERSION from ALL_SOURCE where NAME = 'TEST_DEBUG_ADD'");
            resultSet2.next();
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
            DebugBreakpoint debugBreakpoint = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            // 删除断点
            debugStatement.yacPdbgDeleteBreakpoint(debugBreakpoint);
            // 开始调试
            debugStatement.yacPdbgStart();
            // 添加断点
            DebugBreakpoint debugBreakpoint2 = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            // 展示多有断点
            List<DebugBreakpoint> debugBreakpoints = debugStatement.pdbgShowBreakpoints();
            // 继续执行
            debugStatement.yacPdbgStepInto();
            debugStatement.yacPdbgStepOut();
            debugStatement.yacPdbgStepNext();
            debugStatement.yacPdbgContinue();
            // 退出debug模式
            debugStatement.yacPdbgAbort();
        } catch (SQLException e) {
            if (statement != null) {
                try {
                    statement.close();
                } catch (SQLException e1) {
                    e1.printStackTrace();
                }
            }
            if (debugStatement != null) {
                try {
                    debugStatement.close();
                } catch (SQLException e1) {
                    e1.printStackTrace();
                }
            }
            e.printStackTrace();
        }
    }
}
```
