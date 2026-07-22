## Description

`DebugFrame` represents that it is a core function for debugging stack frame analysis. Its core function is to accurately parse and output the complete information of the program's currently executing stack frame. It can capture key information including the current function call level, stack frame address, function name, parameter list, return address, and local variables in the stack, and output the information in a structured and human-readable format. It is an essential tool for locating call chain anomalies.


## Method

The DebugFrame interface that describes stack information has the following methods:

|Method |Return Type |Notes                                     |
| --------------- | ------------------------ | -------------------------------------------------------- |
| getBlockNo()    | int                      | Get the stack level                                      |
| getClassInfo()  | String                   | Get the information of the stored procedure in the stack |
| getMethodInfo() | String                   | Get the method information of the stack                  |
| getLineNum()    | int                      | Get the line number in the stack                         |

## Example

```java
package quickstart;

import com.yashandb.jdbc.DebugFrame;
import com.yashandb.jdbc.DebugVar;
import com.yashandb.jdbc.Debugger;

import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Wrapper;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class DebugFrameCompleteExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, user, password);
    }

    private static Debugger getDebugger(Connection conn, String procedureName) throws SQLException {
        // Use Oracle-style stored procedure call syntax
        CallableStatement cs = conn.prepareCall("{call " + procedureName + "(?)}");
        cs.setInt(1, 1001);

        // Check if debugging is supported - try to unwrap as Debugger
        if (cs instanceof Wrapper) {
            Wrapper wrapper = (Wrapper) cs;
            if (wrapper.isWrapperFor(Debugger.class)) {
                return (Debugger) wrapper.unwrap(Debugger.class);
            }
        }
        return null;
    }
    public void debugMethodExecution(Connection conn, String procedureName) throws SQLException {
        // Use Oracle-style call syntax
        CallableStatement cs = conn.prepareCall("{call " + procedureName + "(?)}");
        cs.setInt(1, 1001);

        try {
            // Check if debugging is supported
            if (!(cs instanceof Wrapper)) {
                System.out.println("This connection does not support debugging");
                return;
            }

            Wrapper wrapper = (Wrapper) cs;
            if (!wrapper.isWrapperFor(Debugger.class)) {
                System.out.println("This CallableStatement does not support the debugging interface");
                return;
            }

            // Get Debugger instance
            Debugger debugger = (Debugger) wrapper.unwrap(Debugger.class);

            // Start debugging session
            debugger.pdbgStart();

            // Get complete call stack
            List<DebugFrame> stackFrames = debugger.pdbgShowFrames();

            System.out.println("=== Call Stack Analysis ===");
            System.out.println("Total depth: " + stackFrames.size());

            // Analyze each stack frame in detail
            for (int i = 0; i < stackFrames.size(); i++) {
                DebugFrame frame = stackFrames.get(i);

                System.out.println("--- Stack Frame " + i + " ---");
                System.out.println("Block number: " + frame.getBlockNo());

                // getMethodInfo() returns String
                String methodInfo = frame.getMethodInfo();
                System.out.println("Method info: " + methodInfo);

                System.out.println("Current line number: " + frame.getLineNum());

                // getClassInfo() returns String
                String classInfo = frame.getClassInfo();
                if (classInfo != null && !classInfo.isEmpty()) {
                    System.out.println("Class: " + classInfo);
                }
            }

            // Get local variables
            List<DebugVar> localVars = debugger.pdbgShowFrameVariables();
            if (localVars != null && !localVars.isEmpty()) {
                System.out.println("\nLocal variables:");
                for (DebugVar var : localVars) {
                    // getVar() returns the value of the variable
                    System.out.println("  " + var.getName() + " = " + var.getVar());
                }
            }

            // Step debugging
            debugger.pdbgStepNext();

            // Continue execution
            debugger.pdbgContinue();

        } finally {
            // End debugging session
            if (cs instanceof Debugger) {
                ((Debugger) cs).pdbgAbort();
            }
            cs.close();
        }
    }
    
    public List<Integer> findMethodCalls(Debugger debugger, String targetMethod) {
        List<Integer> callLines = new ArrayList<>();
        try {
            List<DebugFrame> frames = debugger.pdbgShowFrames();

            for (DebugFrame frame : frames) {
                if (frame.getMethodInfo() != null && frame.getMethodInfo().contains(targetMethod)) {
                    callLines.add(frame.getLineNum());
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return callLines;
    }
    public void analyzeRecursion(Debugger debugger) {
        Map<String, Integer> methodCallCount = new HashMap<>();
        try {
            List<DebugFrame> frames = debugger.pdbgShowFrames();

            for (DebugFrame frame : frames) {
                String methodName = frame.getMethodInfo();
                if (methodName != null) {
                    methodCallCount.merge(methodName, 1, Integer::sum);
                }
            }

            // Find recursive calls
            for (Map.Entry<String, Integer> entry : methodCallCount.entrySet()) {
                if (entry.getValue() > 1) {
                    System.out.println("Possible recursion: " + entry.getKey() + " appears " + entry.getValue() + " times");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    public void manageBreakpoints(Connection conn, String procedureName) throws SQLException {
        CallableStatement cs = conn.prepareCall("{call " + procedureName + "(?)}");

        if (cs instanceof Wrapper) {
            Wrapper wrapper = (Wrapper) cs;
            if (wrapper.isWrapperFor(Debugger.class)) {
                Debugger debugger = (Debugger) wrapper.unwrap(Debugger.class);
            }
        }
        cs.close();
    }
    public static void main(String[] args) {
        try {
            Connection conn = getConnection();
            DebugFrameCompleteExample example = new DebugFrameCompleteExample();

            System.out.println("Connection successful!");

            conn.close();
        } catch (ClassNotFoundException e) {
            System.err.println("Driver class not found: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            System.err.println("SQL error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

