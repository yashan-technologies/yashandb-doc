The Debug interface is mainly used for debugging stored procedures.

This module's interface is not suitable for ISC Distributed Cluster Deployment.

## Stored Procedure Operation Interface

The YashanDB JDBC driver has the following interfaces for the debugging stored procedure functionality:

|Return Type |Method |Parameters |Description |
| ------------ |---------------------|-------------------| ------------------------- |
| YasDebugCallableStatement     | createDebugStatement<br>(String sql, long objectId, int subprogramId, int version) | \[1\] sql: SQL of the stored procedure to be debugged<br/>\[2\] objectId: objectId of the stored procedure being debugged<br/>\[3\] subprogramId: subprogramId of the stored procedure being debugged<br/>\[4\] version: version of the stored procedure being debugged | Create a debugger session                   |
| YasDebugCallableStatement     | createDebugStatement<br>(String sql)           | sql: SQL of the stored procedure to be debugged                                                  | Create a debugger session                   |
| void                          | yacPdbgStart()                                 |                                                                                                     | Immediately start debugger mode             |
| DebugBreakpoint               | yacPdbgAddBreakpoint<br>(long objectId, int subprogramId, int lineNum) | \[1\] objectId: objectId of the stored procedure for which the breakpoint is set<br/>\[2\] subprogramId: subprogramId of the stored procedure for which the breakpoint is set<br/>\[3\] lineNum: line number of the stored procedure for which the breakpoint is set | Add a breakpoint                            |
| void                          | yacPdbgDeleteBreakpoint<br>(DebugBreakpoint breakpoint) | breakpoint: The breakpoint to be deleted                                                            | Delete a breakpoint                         |
| List<DebugBreakpoint>        | pdbgShowBreakpoints()                           |                                                                                                     | Show all breakpoints                        |
| void                          | yacPdbgDeleteAllBreakpoints()                   |                                                                                                     | Delete all breakpoints                      |
| void                          | yacPdbgStepInto()                               |                                                                                                     | Step into, if a submethod is encountered, enter that submethod execution |
| void                          | yacPdbgStepOut()                                |                                                                                                     | Finish executing the current method and exit |
| void                          | yacPdbgContinue()                               |                                                                                                     | Continue execution until a direct end or encounter a breakpoint |
| void                          | yacPdbgStepNext()                               |                                                                                                     | Execute the next line, does not enter submethods, but submethods will execute |
| List<DebugVar>              | pdbgShowFrameVariables()                        |                                                                                                     | Show all variable information in the current stack |
| List<DebugFrame>            | pdbgShowFrames()                                |                                                                                                     | Show all stack information currently        |
| long                          | getRunObjectId()                               |                                                                                                     | Show the objectId of the currently running stored procedure |
| int                           | getRunSubprogramId()                           |                                                                                                     | Show the subprogramId of the currently running stored procedure |
| int                           | getRunLineNum()                                |                                                                                                     | Show the line number of the currently running stored procedure |
| void                          | yacPdbgAbort()                                 |                                                                                                     | Immediately exit debugger mode              |

## Breakpoint Information Interface

The DebugBreakpoint interface that describes breakpoint information has the following interfaces:

|Return Type |Method |Description |
| -------- | ----------------- | ------------------------------------------ |
| int         | getId()              | Get the global id of the breakpoint        |
| long        | getObjectId()        | Get the objectId of the stored procedure where the breakpoint is located |
| int         | getSubprogramId()    | Get the subprogramId of the stored procedure where the breakpoint is located |
| int         | getLineNum()         | Get the line number of the stored procedure where the breakpoint is located |
| void        | setId(int id)        | Set the global id of the breakpoint, generally returned by the system and does not need to be set |

## Variable Information Interface

The DebugVar interface that describes variable information has the following interfaces:

|Return Type |Method |Description |
| -------- | ------------- | -------------------- |
| int         | getDataType()    | Get the variable type              |
| int         | getBlockNo()     | Get the level of the variable      |
| String      | getName()        | Get the name of the variable       |
| Boolean     | isGlobal()       | Get whether the variable is global  |
| Object      | getVar           | Get the value of the variable      |

## Stack Information Interface

The DebugFrame interface that describes stack information has the following interfaces:

|Return Type |Method |Description |
| -------- | --------------- | ---------------------- |
| int         | getBlockNo()      | Get the stack level                  |
| String      | getClassInfo()    | Get the information of the stored procedure in the stack |
| String      | getMethodInfo()   | Get the method information of the stack |
| int         | getLineNum()      | Get the line number in the stack     |

***Example***: Debugging a stored procedure based on JDBC interface.

```java
package jdbc0;

//JdbcDebugExample.java
//Demonstrates the main steps for debugging stored procedures based on JDBC

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
    // Create the database connection.
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://127.0.0.1:1688/ya";
        Connection conn = null;
        try {
            // Load the database driver.
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        try {
            // Create the database connection.
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }
 
    // Debugging stored procedure
    public static void debugMode(Connection conn) throws SQLException {
        Statement statement = null;
        YasDebugCallableStatement debugStatement = null;
        try {
            statement = conn.createStatement();
            // Create the stored procedure for debugging
            statement.execute("create or replace procedure test_debug_add(p1 in int, p2 in int, p3 out int) is\n"
                    + "begin\n"
                    + "    p3 := p1 + p2;\n"
                    + "    p3 := p3 + p2;\n"
                    + "end;");
            // Get the stored procedure's OBJECT_ID, SUBPROGRAM_ID
            ResultSet resultSet = statement.executeQuery("select OBJECT_ID,SUBPROGRAM_ID from dba_procedures "
                    + "where OBJECT_NAME = 'TEST_DEBUG_ADD'");
            resultSet.next();
            long objectId = resultSet.getLong(1);
            int subprogramId = resultSet.getInt(2);
            // Get the stored procedure's VERSION
            ResultSet resultSet2 = statement.executeQuery("select VERSION from ALL_SOURCE where NAME = 'TEST_DEBUG_ADD'");
            resultSet2.next();
            int version = resultSet2.getInt(1);
            // Create debug statement
            debugStatement = ((YasConnection)conn).createDebugStatement(
                    "begin test_debug_add(?, ?, ?); end;",objectId, subprogramId, version);
            // Set parameters and output parameter
            debugStatement.clearParameters();
            debugStatement.setInt(1, 1);
            debugStatement.setInt(2, 2);
            debugStatement.registerOutParameter(3, YasTypes.INTEGER);
            // Add a breakpoint
            DebugBreakpoint debugBreakpoint = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            // Delete a breakpoint
            debugStatement.yacPdbgDeleteBreakpoint(debugBreakpoint);
            // Start debugging
            debugStatement.yacPdbgStart();
            // Add another breakpoint
            DebugBreakpoint debugBreakpoint2 = debugStatement.yacPdbgAddBreakpoint(objectId, subprogramId, 4);
            // Show all breakpoints
            List<DebugBreakpoint> debugBreakpoints = debugStatement.pdbgShowBreakpoints();
            // Continue execution
            debugStatement.yacPdbgStepInto();
            debugStatement.yacPdbgStepOut();
            debugStatement.yacPdbgStepNext();
            debugStatement.yacPdbgContinue();
            // Exit debug mode
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
