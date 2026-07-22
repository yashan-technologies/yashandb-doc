## Description

`DebugVar` represents that it is a utility function for debugging variable monitoring. Its core function is to accurately output or monitor key information of specified variables. It can proactively capture information such as a variable's name, current value, data type, and optional memory address, and output it in a human-readable format to the debug console, log file or debugger panel. It is a commonly used tool for developers to quickly locate variable anomalies (for example, unexpected values, type errors).


## Method

The DebugVar interface that describes variable information has the following methods:

|Method |Return Type |Notes               |
| --------------- | ------------------------ | ---------------------------------- |
| getDataType()   | int                      | Get the variable type              |
| getBlockNo()    | int                      | Get the level of the variable      |
| getName()       | String                   | Get the name of the variable       |
| isGlobal()      | Boolean                  | Get whether the variable is global |
| getVar          | Object                   | Get the value of the variable      |

## Example

```java
package quickstart;

import com.yashandb.jdbc.DebugVar;
import com.yashandb.jdbc.DebugFrame;
import com.yashandb.jdbc.YasDebugCallableStatement;
import com.yashandb.jdbc.Debugger;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.List;


 // Demonstrates how to debug stored procedures in YashanDB
public class DebugVarCompleteExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, user, password);
    }

    //  Debug stored procedure example
    public void debugProcedure(Connection conn, String procName) throws SQLException {
        // Create debug statement through Connection
        YasDebugCallableStatement debugger = conn.createDebugStatement("{call " + procName + "(?, ?, ?)}");

        try {
            debugger.setInt(1, 1001);
            debugger.setDouble(2, 50000.0);
            debugger.registerOutParameter(3, java.sql.Types.DOUBLE);

            // Start debugging mode
            debugger.pdbgStart();

            if (!debugger.isDebugOn()) {
                System.out.println("Debug mode failed to start");
                return;
            }

            // Analyze all variables
            analyzeAllVariables(debugger);

            // View call stack frames
            viewCallStack(debugger);

            // Step execution example
            stepThrough(debugger, 5);

        } finally {
            // Abort debugging
            if (debugger.isDebugOn()) {
                debugger.pdbgAbort();
            }
            debugger.close();
        }
    }

    // Analyze all variables
    private void analyzeAllVariables(YasDebugCallableStatement debugger) throws SQLException {
        List<DebugVar> allVars = debugger.pdbgShowFrameVariables();

        System.out.println("=== Variable Analysis Report ===");
        System.out.println("Total variables: " + allVars.size());

        for (DebugVar var : allVars) {
            // Get variable name
            String varName = var.getName();
            // Get variable value - use getVar() instead of getValue()
            Object value = var.getVar();
            // Get data type - returns an int type code
            int dataType = var.getDataType();
            // Get block number
            int blockNo = var.getBlockNo();
            // Determine whether it is a global variable
            Boolean isGlobal = var.isGlobal();

            System.out.println("Variable name: " + varName);
            System.out.println("  Value: " + value);
            System.out.println("  Type code: " + dataType + " (" + getTypeName(dataType) + ")");
            System.out.println("  Block no: " + blockNo);
            System.out.println("  Global: " + (isGlobal != null && isGlobal ? "Yes" : "No"));
        }
    }
    
    private void viewCallStack(YasDebugCallableStatement debugger) throws SQLException {
        List<DebugFrame> frames = debugger.pdbgShowFrames();

        System.out.println("=== Call Stack Frames ===");
        System.out.println("Frame count: " + frames.size());

        for (int i = 0; i < frames.size(); i++) {
            DebugFrame frame = frames.get(i);
            System.out.println("Frame #" + i);
            System.out.println("  Class info: " + frame.getClassInfo());
            System.out.println("  Method info: " + frame.getMethodInfo());
            System.out.println("  Line number: " + frame.getLineNum());
        }
    }

    private void stepThrough(YasDebugCallableStatement debugger, int stepCount) throws SQLException {
        System.out.println("=== Step Execution ===");

        for (int i = 0; i < stepCount && debugger.isDebugOn(); i++) {
            // Get current position
            long objectId = debugger.getRunObjectId();
            int subprogramId = debugger.getRunSubprogramId();
            int lineNum = debugger.getRunLineNum();

            System.out.println("Step " + (i + 1) + ": object=" + objectId + ", subprogram=" + subprogramId + ", line=" + lineNum);

            // Step execution - use pdbgStepNext
            debugger.pdbgStepNext();

            // Get variables after each step
            List<DebugVar> vars = debugger.pdbgShowFrameVariables();
            System.out.println("  Current variables: " + vars.size());
        }
    }
    

    public void addBreakpointExample(Connection conn, String procName, int lineNum) throws SQLException {
        YasDebugCallableStatement debugger = conn.createDebugStatement("{call " + procName + "()}");

        try {
            // Add breakpoint
            debugger.pdbgAddBreakpoint(1000L, 1, lineNum);

            // View all breakpoints
            System.out.println("=== Breakpoint List ===");
            List<?> breakpoints = debugger.pdbgShowBreakpoints();
            System.out.println("Breakpoint count: " + breakpoints.size());

        } finally {
            if (debugger.isDebugOn()) {
                debugger.pdbgAbort();
            }
            debugger.close();
        }
    }

    public void simpleDebugFlow(Connection conn) throws SQLException {
        // Create debug statement
        YasDebugCallableStatement debugger = conn.createDebugStatement("SELECT * FROM DUAL");

        try {
            // Start debugging
            debugger.pdbgStart();

            if (debugger.isDebugOn()) {
                System.out.println("Debugging started");

                // Get variables
                List<DebugVar> vars = debugger.pdbgShowFrameVariables();
                System.out.println("Variable count: " + vars.size());

                // Continue execution
                debugger.pdbgContinue();

                // Get call stack
                List<DebugFrame> frames = debugger.pdbgShowFrames();
                System.out.println("Frame count: " + frames.size());
            }

        } finally {
            // Terminate debugging
            debugger.pdbgAbort();
            debugger.close();
        }
    }

    private String getTypeName(int typeCode) {
        // Common types in java.sql.Types
        switch (typeCode) {
            case java.sql.Types.CHAR:
            case java.sql.Types.VARCHAR:
                return "VARCHAR";
            case java.sql.Types.NUMERIC:
            case java.sql.Types.DECIMAL:
                return "NUMBER";
            case java.sql.Types.INTEGER:
                return "INTEGER";
            case java.sql.Types.FLOAT:
            case java.sql.Types.REAL:
                return "FLOAT";
            case java.sql.Types.DOUBLE:
                return "DOUBLE";
            case java.sql.Types.DATE:
            case java.sql.Types.TIME:
            case java.sql.Types.TIMESTAMP:
                return "DATE/TIME";
            case java.sql.Types.BLOB:
                return "BLOB";
            case java.sql.Types.CLOB:
                return "CLOB";
            default:
                return "UNKNOWN(" + typeCode + ")";
        }
    }

    public static void main(String[] args) {
        try (Connection conn = getConnection()) {
            DebugVarCompleteExample example = new DebugVarCompleteExample();

             System.out.println("=== Example 1: Simple Debug Flow ===");
            example.simpleDebugFlow(conn);

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

