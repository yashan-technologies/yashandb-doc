Debug接口主要用于调试存储过程。

此模块的接口不适用于存算一体分布式集群部署。

## 操作存储过程接口

YashanDB JDBC驱动对调试存储过程功能有如下接口：

|  返回类型| 方法| 参数| 说明|
| ------------ |---------------------|-------------------| ------------------------- |
| YasDebugCallableStatement | createDebugStatement<br>(String sql, long objectId, int subprogramId, int version) | \[1\] sql：调试存储过程的sql<br/>\[2\] objectId：被调试存储过程的objectId<br/>\[3\] subprogramId：被调试存储过程的subprogramId<br/>\[4\] version：被调试存储过程的version | 创建调试器会话                                         |
| YasDebugCallableStatement | createDebugStatement<br>(String sql)                                               | SQL：调试存储过程的sql                                                                                                                            | 创建调试器会话                                         |
| void                      | yacPdbgStart()                                                                        |                                                                                                                                            | 立即开始调试器模式                                     |
| DebugBreakpoint           | yacPdbgAddBreakpoint<br>(long objectId, int subprogramId, int lineNum)                | \[1\] objectId：断点所造存储过程的objectId<br/>\[2\] subprogramId：断点所造存储过程的subprogramId<br/>\[3\] lineNum：断点所造存储过程的行号                             | 增加断点                                               |
| void                      | yacPdbgDeleteBreakpoint<br>(DebugBreakpoint breakpoint)                               | breakpoint：要被删除的断点                                                                                                                        | 删除断点                                               |
| List\<DebugBreakpoint>     | pdbgShowBreakpoints()                                                              |                                                                                                                                            | 展示所有断点                                           |
| void                      | yacPdbgDeleteAllBreakpoints()                                                         |                                                                                                                                            | 删除所有断点                                           |
| void                      | yacPdbgStepInto()                                                                     |                                                                                                                                            | 单步执行，如果遇到子方法，进入子方法执行               |
| void                      | yacPdbgStepOut()                                                                      |                                                                                                                                            | 执行完当前方法并退出                                   |
| void                      | yacPdbgContinue()                                                                     |                                                                                                                                            | 继续执行，直到直接结束或者遇到断点                     |
| void                      | yacPdbgStepNext()                                                                     |                                                                                                                                            | 执行下一行，遇到子方法不会进入子方法，但是子方法会执行 |
| List\<DebugVar>            | pdbgShowFrameVariables()                                                           |                                                                                                                                            | 展示当前堆栈的所有变量信息                             |
| List\<DebugFrame>          | pdbgShowFrames()                                                                   |                                                                                                                                            | 展示当前所有的堆栈信息                                 |
| long                      | getRunObjectId()                                                                   |                                                                                                                                            | 展示当前运行存储过程的objectId                         |
| int                       | getRunSubprogramId()                                                               |                                                                                                                                            | 展示当前运行存储过程的subprogramId                     |
| int                       | getRunLineNum()                                                                    |                                                                                                                                            | 展示当前运行存储过程的行号                             |
| void                      | yacPdbgAbort()                                                                        |                                                                                                                                            | 立即退出调试器模式                                     |

## 断点信息接口

描述断点信息的DebugBreakpoint接口有如下接口：

|  返回类型| 方法| 说明|
| -------- | ----------------- | ------------------------------------------ |
| int      | getId()           | 获取断点的全局id                           |
| long     | getObjectId()     | 获取断点所在存储过程的objectId             |
| int      | getSubprogramId() | 获取断点所在存储过程的subprogramId         |
| int      | getLineNum()      | 获取断点所在存储过程的行号                 |
| void     | setId(int id)     | 设置断点的全局id，一般由系统返回不需要设置 |

## 变量信息接口

描述变量信息的DebugVar接口有如下接口：

|  返回类型| 方法| 说明|
| -------- | ------------- | -------------------- |
| int      | getDataType() | 获取变量类型         |
| int      | getBlockNo()  | 获取变量所在层级     |
| String   | getName()     | 获取变量名称         |
| Boolean  | isGlobal()    | 获取变量是否全局变量 |
| Object   | getVar        | 获取变量值           |

## 堆栈信息接口

描述堆栈信息的DebugFrame接口有如下接口：

|  返回类型| 方法| 说明|
| -------- | --------------- | ---------------------- |
| int      | getBlockNo()    | 获取堆栈层级           |
| String   | getClassInfo()  | 获取堆栈的存储过程信息 |
| String   | getMethodInfo() | 获取堆栈的方法信息     |
| int      | getLineNum()    | 获取堆栈行号           |

示例：基于JDBC接口调试存储过程。

```java
package jdbc0;

//JdbcDebugExample.java
//演示基于JDBC调试存储过程的主要步骤

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
    //创建数据库连接。
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://127.0.0.1:1688/ya";
        Connection conn = null;
        try {
            //加载数据库驱动。
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        try {
            //创建数据库连接。
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }
 
    //调试存储过程
    public static void debugMode(Connection conn) throws SQLException {
        Statement statement = null;
        YasDebugCallableStatement debugStatement = null;
        try {
            statement = conn.createStatement();
            //创建调试的存储过程
            statement.execute("create or replace procedure test_debug_add(p1 in int, p2 in int, p3 out int) is\n"
                    + "begin\n"
                    + "    p3 := p1 + p2;\n"
                    + "    p3 := p3 + p2;\n"
                    + "end;");
            //获取存储过程的OBJECT_ID,SUBPROGRAM_ID
            ResultSet resultSet = statement.executeQuery("select OBJECT_ID,SUBPROGRAM_ID from dba_procedures "
                    + "where OBJECT_NAME = 'TEST_DEBUG_ADD'");
            resultSet.next();
            long objectId = resultSet.getLong(1);
            int subprogramId = resultSet.getInt(2);
            //获取存储过程的VERSION
            ResultSet resultSet2 = statement.executeQuery("select VERSION from ALL_SOURCE where NAME = 'TEST_DEBUG_ADD'");
            resultSet2.next();
            int version = resultSet2.getInt(1);
            //创建调试statement
            debugStatement = ((YasConnection)conn).createDebugStatement(
                    "begin test_debug_add(?, ?, ?); end;",objectId, subprogramId, version);
            //设置参数和出参
            debugStatement.clearParameters();
            debugStatement.setInt(1, 1);
            debugStatement.setInt(2, 2);
            debugStatement.registerOutParameter(3, YasTypes.INTEGER);
            //添加断点
            DebugBreakpoint debugBreakpoint = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            //删除断点
            debugStatement.yacPdbgDeleteBreakpoint(debugBreakpoint);
            //开始调试
            debugStatement.yacPdbgStart();
            //添加断点
            DebugBreakpoint debugBreakpoint2 = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            //展示多有断点
            List<DebugBreakpoint> debugBreakpoints = debugStatement.pdbgShowBreakpoints();
            //继续执行
            debugStatement.yacPdbgStepInto();
            debugStatement.yacPdbgStepOut();
            debugStatement.yacPdbgStepNext();
            debugStatement.yacPdbgContinue();
            //退出debug模式
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
