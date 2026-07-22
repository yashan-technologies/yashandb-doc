## Description

`DebugBreakpoint` represents that it is a core function for debugger triggering. Its function is to proactively insert a debug breakpoint into the code execution flow. When the program runs to the position where this function is called, it will pause execution and trigger a debugger interrupt, allowing developers to view key information such as variable values, call stacks, and memory status at that moment. It is an important tool for manually controlling the debugging process.


## Method

The DebugBreakpoint interface describing breakpoint information has the following methods:

|Method   |Return Type |Notes                                         |
| ----------------- | ------------------------ | ------------------------------------------------------------ |
| getId()           | int                      | Get the global id of the breakpoint                          |
| getObjectId()     | long                     | Get the objectId of the stored procedure where the breakpoint is located |
| getSubprogramId() | int                      | Get the subprogramId of the stored procedure where the breakpoint is located |
| getLineNum()      | int                      | Get the line number of the stored procedure where the breakpoint is located |
| setId(int id)     | void                     | Set the global id of the breakpoint, generally returned by the system and does not need to be set |

## Example

```java
package quickstart;

import com.yashandb.jdbc.DebugBreakpoint;
import com.yashandb.jdbc.DebugFrame;
import com.yashandb.jdbc.DebugVar;
import com.yashandb.jdbc.Debugger;
import com.yashandb.jdbc.YasDebugCallableStatement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Types;
import java.util.List;

public class JdbcDebuggerExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, user, password);
    }

    // Complete breakpoint debugging example
    public void debugStoredProcedure(Connection conn) throws SQLException {
        // 1. Create debuggable CallableStatement
        YasDebugCallableStatement stmt = (YasDebugCallableStatement)
            conn.prepareCall("{call CALCULATE_TAX(?, ?)}");

        // 2. Get Debugger interface
        Debugger debugger = stmt;

        // Set input parameters before adding breakpoints
        stmt.setDouble(1, 75000.0);
        stmt.registerOutParameter(2, Types.DOUBLE);

        try {
            // 3. Add breakpoints (need objectId, subprogramId, lineNum)
            // You need to query the objectId and subprogramId from DBA_PROCEDURES first
            // Example: SELECT OBJECT_ID, SUBPROGRAM_ID FROM DBA_PROCEDURES WHERE OWNER = 'USER' AND OBJECT_NAME = 'CALCULATE_TAX'
            long objectId = 12345;  // Replace with actual OBJECT_ID from database
            int subprogramId = 0;   // Usually 0 for top-level procedure
            DebugBreakpoint bp1 = debugger.pdbgAddBreakpoint(objectId, subprogramId, 10);
            DebugBreakpoint bp2 = debugger.pdbgAddBreakpoint(objectId, subprogramId, 25);

            // 4. Get breakpoint information
            System.out.println("Breakpoint 1 information:");
            System.out.println("  ID: " + bp1.getId());
            System.out.println("  Object ID: " + bp1.getObjectId());
            System.out.println("  Subprogram ID: " + bp1.getSubprogramId());
            System.out.println("  Line number: " + bp1.getLineNum());

            // 5. Start debugging - this will execute and stop at breakpoints
            debugger.pdbgStart();
            // 6. Check if debug mode is on
            if (debugger.isDebugOn()) {
                System.out.println("Debug mode is active");
                System.out.println("  Running at objectId: " + debugger.getRunObjectId());
                System.out.println("  Running at subprogramId: " + debugger.getRunSubprogramId());
                System.out.println("  Running at lineNum: " + debugger.getRunLineNum());

                // 7. Inspect variables at breakpoint
                List<DebugVar> vars = debugger.pdbgShowFrameVariables();
                System.out.println("Variables at breakpoint:");
                for (DebugVar var : vars) {
                    System.out.println("  " + var.getName() + " = " + var.getVar());
                }

                // 8. Show stack frames
                List<DebugFrame> frames = debugger.pdbgShowFrames();
                System.out.println("Stack frames:");
                for (DebugFrame frame : frames) {
                    System.out.println("  Frame: " + frame);
                }

                // 9. Continue execution (step by step)
                // Step into
                // debugger.pdbgStepInto();

                // Step out
                // debugger.pdbgStepOut();

                // Step next
                // debugger.pdbgStepNext();

                // Continue to next breakpoint or end
                debugger.pdbgContinue();
            }

            // 10. Get output parameter after execution
            double tax = stmt.getDouble(2);
            System.out.println("Tax calculated: " + tax);

        } finally {
            // 11. Clean up breakpoints
            debugger.pdbgDeleteAllBreakpoints();
            stmt.close();
        }
    }

    // Example: How to get objectId and subprogramId from database
    public void getProcedureInfo(Connection conn) throws SQLException {
        String procName = "CALCULATE_TAX";

        // Query procedure info
        java.sql.PreparedStatement ps = conn.prepareStatement(
            "SELECT OBJECT_ID, SUBPROGRAM_ID FROM DBA_PROCEDURES " +
            "WHERE OWNER = ? AND OBJECT_NAME = ?"
        );
        ps.setString(1, "SALES");  // owner
        ps.setString(2, procName);

        java.sql.ResultSet rs = ps.executeQuery();
        if (rs.next()) {
            long objectId = rs.getLong("OBJECT_ID");
            int subprogramId = rs.getInt("SUBPROGRAM_ID");
            System.out.println("Object ID: " + objectId + ", Subprogram ID: " + subprogramId);
        }
        rs.close();
        ps.close();
    }
}
```
