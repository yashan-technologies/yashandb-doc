## Description

`YasConnection` represents that it is a class constructor method for connecting to the YashanDB database. Its function is to establish a network connection between an application and the YashanDB database, serving as the fundamental entry point for operating the YashanDB database. All operations such as querying, writing, and modifying data in YashanDB require the creation of a valid connection object through this function first.


## Method

The YashanDB JDBC driver has the following methods for connecting to the YashanDB database:

|Method    |Return Type  |Notes|
| ------------------ | ------------------------- | ------------------------------------------------------------ |
| createDebugStatement<br/>(String plsql, long objectId, int subprogramId, int version) | YasDebugCallableStatement | Create a debugger session<br/>ObjectId and subprogramId are obtained by querying the dba_procedures view. <br/>Version is obtained by querying the ALL_SOURCE view.<br/>- plsql: Plsql of the stored procedure to be debugged<br/>- objectId: objectId of the stored procedure being debugged<br/>- subprogramId: subprogramId of the stored procedure being debugged<br/>-  version: version of the stored procedure being debugged |
| createDebugStatement<br/>(String plsql)  | YasDebugCallableStatement | Create a debugger session<br/>plsql: plsql of the stored procedure to be debugged |
| yacPdbgStart()   | void       | Immediately start debugger mode           |
| yacPdbgAddBreakpoint<br/>(long objectId, int subprogramId, int lineNum) | DebugBreakpoint   | Add a breakpoint <br/> - objectId: objectId of the stored procedure for which the breakpoint is set<br/>- subprogramId: subprogramId of the stored procedure for which the breakpoint is set<br/>- lineNum: line number of the stored procedure for which the breakpoint is set |
| yacPdbgDeleteBreakpoint<br/>(DebugBreakpoint breakpoint)     | void                      | breakpoint: The breakpoint to be deleted                     |
| pdbgShowBreakpoints()                                        | List<DebugBreakpoint>     | Show all breakpoints |
| yacPdbgDeleteAllBreakpoints()                                | void                      | Delete all breakpoints |
| yacPdbgStepInto()                                            | void                      | Step into, if a submethod is encountered, enter that submethod execution |
| yacPdbgStepOut()                                             | void                      | Finish executing the current method and exit |
| yacPdbgContinue()                                            | void                      | Continue execution until a direct end or encounter a breakpoint |
| yacPdbgStepNext()                                            | void                      | Execute the next line, does not enter submethods, but submethods will execute |
| pdbgShowFrameVariables()                                     | List<DebugVar>            | Show all variable information in the current stack |
| pdbgShowFrames()                                             | List<DebugFrame>          | Show all stack information currently |
| getRunObjectId()                                             | long                      | Show the objectId of the currently running stored procedure |
| getRunSubprogramId()                                         | int                       | Show the subprogramId of the currently running stored procedure |
| getRunLineNum()                                              | int                       | Show the line number of the currently running stored procedure |
| yacPdbgAbort()                                               | void                      | Immediately exit debugger mode |

## Example

```java
package quickstart;

// JdbcDebugExample.java
// Demonstrates the main steps for debugging stored procedures based on JDBC

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
    public static Connection getConnection() {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
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
            conn = DriverManager.getConnection(sourceURL, user, password);
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
            debugStatement = ((YasConnection) conn).createDebugStatement(
                    "begin test_debug_add(?, ?, ?); end;", objectId, subprogramId, version);
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
