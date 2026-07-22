## 描述

`DebugFrame`是调试栈帧分析类核心函数，核心作用是精准解析并输出程序当前执行栈帧的完整信息，它能捕获当前函数调用层级、栈帧地址、函数名、参数列表、返回地址、栈内局部变量等关键信息，并以结构化、易读的格式输出，是定位调用链路异常的核心工具。


## 方法

描述堆栈信息的DebugFrame接口有如下方法：

| 方法| 返回类型| 备注|
| --------------- | ------------------------ | -------------------------------------------------------- |
| getBlockNo()    | int                      | 获取堆栈层级                                             |
| getClassInfo()  | String                   | 获取堆栈的存储过程信息                                   |
| getMethodInfo() | String                   | 获取堆栈的方法信息                                       |
| getLineNum()    | int                      | 获取堆栈行号                                             |

## 示例

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
        // 使用Oracle风格的存储过程调用语法
        CallableStatement cs = conn.prepareCall("{call " + procedureName + "(?)}");
        cs.setInt(1, 1001);

        // 检查是否支持调试 - 尝试unwrap为Debugger
        if (cs instanceof Wrapper) {
            Wrapper wrapper = (Wrapper) cs;
            if (wrapper.isWrapperFor(Debugger.class)) {
                return (Debugger) wrapper.unwrap(Debugger.class);
            }
        }
        return null;
    }
    public void debugMethodExecution(Connection conn, String procedureName) throws SQLException {
        // 使用Oracle风格调用语法
        CallableStatement cs = conn.prepareCall("{call " + procedureName + "(?)}");
        cs.setInt(1, 1001);

        try {
            // 检查是否支持调试功能
            if (!(cs instanceof Wrapper)) {
                System.out.println("此连接不支持调试功能");
                return;
            }

            Wrapper wrapper = (Wrapper) cs;
            if (!wrapper.isWrapperFor(Debugger.class)) {
                System.out.println("此CallableStatement不支持调试接口");
                return;
            }

            // 获取Debugger实例
            Debugger debugger = (Debugger) wrapper.unwrap(Debugger.class);

            // 启动调试会话
            debugger.pdbgStart();

            // 获取完整调用栈
            List<DebugFrame> stackFrames = debugger.pdbgShowFrames();

            System.out.println("=== 调用栈分析 ===");
            System.out.println("总深度: " + stackFrames.size());

            // 详细分析每个栈帧
            for (int i = 0; i < stackFrames.size(); i++) {
                DebugFrame frame = stackFrames.get(i);

                System.out.println("\n--- 栈帧 " + i + " ---");
                System.out.println("块编号: " + frame.getBlockNo());

                // getMethodInfo() 返回 String
                String methodInfo = frame.getMethodInfo();
                System.out.println("方法信息: " + methodInfo);

                System.out.println("当前行号: " + frame.getLineNum());

                // getClassInfo() 返回 String
                String classInfo = frame.getClassInfo();
                if (classInfo != null && !classInfo.isEmpty()) {
                    System.out.println("类: " + classInfo);
                }
            }

            // 获取局部变量
            List<DebugVar> localVars = debugger.pdbgShowFrameVariables();
            if (localVars != null && !localVars.isEmpty()) {
                System.out.println("\n局部变量:");
                for (DebugVar var : localVars) {
                    // getVar() 返回变量的值
                    System.out.println("  " + var.getName() + " = " + var.getVar());
                }
            }

            // 单步调试
            debugger.pdbgStepNext();

            // 继续执行
            debugger.pdbgContinue();

        } finally {
            // 结束调试会话
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

            // 找出递归调用
            for (Map.Entry<String, Integer> entry : methodCallCount.entrySet()) {
                if (entry.getValue() > 1) {
                    System.out.println("可能的递归: " + entry.getKey() + " 出现 " + entry.getValue() + " 次");
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

            System.out.println("连接成功!");

            conn.close();
        } catch (ClassNotFoundException e) {
            System.err.println("驱动类未找到: " + e.getMessage());
            e.printStackTrace();
        } catch (SQLException e) {
            System.err.println("SQL错误: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

