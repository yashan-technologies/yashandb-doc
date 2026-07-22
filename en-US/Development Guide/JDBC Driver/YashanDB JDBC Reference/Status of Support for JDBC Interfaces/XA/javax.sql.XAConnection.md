## Description

The XAConnection interface represents that it is used to obtain an XAResource object.

`javax.sql.XAConnection` represents that it is the core interface in the JDBC specification for supporting distributed transactions (XA transactions). It inherits from the parent interface of `java.sql.Connection`. `XAConnection` can be associated with an XA transaction manager to perform commit and rollback operations on the database, ensuring data consistency in distributed scenarios.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/javax/sql/XAConnection.html).

## Method

The YashanDB JDBC driver has the following methods for the XAConnection interface:

|Method |Return Type |
|-------------------|----------------------------------|
| getXAResource()   | javax.transaction.xa.XAResource  |

> **Note**: 
>
> The XAConnection interface is a subinterface of the PooledConnection interface, so all interfaces in PooledConnection can be used.

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
        ResultSet resultSet = null;
        ResultSet resultSet2 = null;
        try {
            statement = conn.createStatement();
            // Create the stored procedure for debugging
            statement.execute("create or replace procedure test_debug_add(p1 in int, p2 in int, p3 out int) is\n"
                    + "begin\n"
                    + "    p3 := p1 + p2;\n"
                    + "    p3 := p3 + p2;\n"
                    + "end;");
            // Get the stored procedure's OBJECT_ID, SUBPROGRAM_ID
            resultSet = statement.executeQuery("select OBJECT_ID,SUBPROGRAM_ID from dba_procedures "
                    + "where OBJECT_NAME = 'TEST_DEBUG_ADD'");
            if (resultSet.next()) {
                long objectId = resultSet.getLong(1);
                int subprogramId = resultSet.getInt(2);
                // Get the stored procedure's VERSION
                resultSet2 = statement.executeQuery("select VERSION from ALL_SOURCE where NAME = 'TEST_DEBUG_ADD'");
                if (resultSet2.next()) {
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
                    DebugBreakpoint debugBreakpoint = debugStatement.pdbgAddBreakpoint(objectId, subprogramId, 4);
                    // Delete a breakpoint
                    debugStatement.pdbgDeleteBreakpoint(debugBreakpoint);
                    
                    // Start debugging
                    debugStatement.pdbgStart();
                    // Add another breakpoint
                    DebugBreakpoint debugBreakpoint2 = debugStatement.pdbgAddBreakpoint(objectId, subprogramId, 4);
                    // Show all breakpoints
                    List<DebugBreakpoint> debugBreakpoints = debugStatement.pdbgShowBreakpoints();
                    // Continue execution
                    debugStatement.pdbgStepInto();
                    debugStatement.pdbgStepOut();
                    debugStatement.pdbgStepNext();
                    debugStatement.pdbgContinue();
                    // Exit debug mode
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

