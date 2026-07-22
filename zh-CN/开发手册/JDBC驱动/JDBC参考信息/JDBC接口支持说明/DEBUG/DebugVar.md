## 描述

`DebugVar`是调试变量监控类工具函数，核心作用是精准输出或监控指定变量的关键信息，它能主动捕获变量的名称、当前值、数据类型、内存地址（可选）等信息，并以易读的格式输出到调试控制台、日志文件或调试器面板，是开发者快速定位变量异常（如值不符合预期、类型错误）的常用工具。


## 方法

描述变量信息的DebugVar接口有如下方法：

| 方法| 返回类型| 备注|
| --------------- | ------------------------ | ---------------------------------- |
| getDataType()   | int                      | 获取变量类型                       |
| getBlockNo()    | int                      | 获取变量所在层级                   |
| getName()       | String                   | 获取变量名称                       |
| isGlobal()      | Boolean                  | 获取变量是否全局变量               |
| getVar          | Object                   | 获取变量值                         |

## 示例

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


 // YashanDB 存储过程调试示例
public class DebugVarCompleteExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, user, password);
    }

     // 调试存储过程示例
    public void debugProcedure(Connection conn, String procName) throws SQLException {
        // 通过Connection创建调试语句
        YasDebugCallableStatement debugger = conn.createDebugStatement("{call " + procName + "(?, ?, ?)}");

        try {
            // 设置输入参数
            debugger.setDouble(2, 50000.0);
            debugger.registerOutParameter(3, java.sql.Types.DOUBLE);

            // 启动调试模式
            debugger.pdbgStart();

            if (!debugger.isDebugOn()) {
                System.out.println("Debug mode failed to start");
                return;
            }

            // 分析所有变量
            analyzeAllVariables(debugger);

            // 查看调用栈帧
            viewCallStack(debugger);

            // 单步执行示例
            stepThrough(debugger, 5);

        } finally {
            // 终止调试
            if (debugger.isDebugOn()) {
                debugger.pdbgAbort();
            }
            debugger.close();
        }
    }

    // 分析所有变量
    private void analyzeAllVariables(YasDebugCallableStatement debugger) throws SQLException {
        List<DebugVar> allVars = debugger.pdbgShowFrameVariables();

        System.out.println("=== 变量分析报告 ===");
        System.out.println("总变量数: " + allVars.size());

        for (DebugVar var : allVars) {
            // 获取变量名
            String varName = var.getName();
            // 获取变量值 - 使用getVar()而不是getValue()
            Object value = var.getVar();
            // 获取数据类型 - 返回int类型码
            int dataType = var.getDataType();
            // 获取块号
            int blockNo = var.getBlockNo();
            // 是否全局变量
            Boolean isGlobal = var.isGlobal();

            System.out.println("\n变量名: " + varName);
            System.out.println("  值: " + value);
            System.out.println("  类型代码: " + dataType + " (" + getTypeName(dataType) + ")");
            System.out.println("  块号: " + blockNo);
            System.out.println("  全局: " + (isGlobal != null && isGlobal ? "是" : "否"));
        }
    }
    
    private void viewCallStack(YasDebugCallableStatement debugger) throws SQLException {
        List<DebugFrame> frames = debugger.pdbgShowFrames();

        System.out.println("\n=== 调用栈帧 ===");
        System.out.println("栈帧数量: " + frames.size());

        for (int i = 0; i < frames.size(); i++) {
            DebugFrame frame = frames.get(i);
            System.out.println("\n帧 #" + i);
            System.out.println("  类信息: " + frame.getClassInfo());
            System.out.println("  方法信息: " + frame.getMethodInfo());
            System.out.println("  行号: " + frame.getLineNum());
        }
    }

    private void stepThrough(YasDebugCallableStatement debugger, int stepCount) throws SQLException {
        System.out.println("\n=== 单步执行 ===");

        for (int i = 0; i < stepCount && debugger.isDebugOn(); i++) {
            // 获取当前位置
            long objectId = debugger.getRunObjectId();
            int subprogramId = debugger.getRunSubprogramId();
            int lineNum = debugger.getRunLineNum();

            System.out.println("步进 " + (i + 1) + ": 对象=" + objectId + ", 子程序=" + subprogramId + ", 行号=" + lineNum);

            // 单步执行 - 使用pdbgStepNext
            debugger.pdbgStepNext();

            // 在每步后获取变量
            List<DebugVar> vars = debugger.pdbgShowFrameVariables();
            System.out.println("  当前变量数: " + vars.size());
        }
    }
    

    public void addBreakpointExample(Connection conn, String procName, int lineNum) throws SQLException {
        YasDebugCallableStatement debugger = conn.createDebugStatement("{call " + procName + "()}");

        try {
            // 添加断点
            debugger.pdbgAddBreakpoint(1000L, 1, lineNum);

            // 查看所有断点
            System.out.println("=== 断点列表 ===");
            List<?> breakpoints = debugger.pdbgShowBreakpoints();
            System.out.println("断点数量: " + breakpoints.size());

        } finally {
            if (debugger.isDebugOn()) {
                debugger.pdbgAbort();
            }
            debugger.close();
        }
    }

    public void simpleDebugFlow(Connection conn) throws SQLException {
        // 创建调试语句
        YasDebugCallableStatement debugger = conn.createDebugStatement("SELECT * FROM DUAL");

        try {
            // 启动调试
            debugger.pdbgStart();

            if (debugger.isDebugOn()) {
                System.out.println("调试已启动");

                // 获取变量
                List<DebugVar> vars = debugger.pdbgShowFrameVariables();
                System.out.println("变量数量: " + vars.size());

                // 继续执行
                debugger.pdbgContinue();

                // 获取调用栈
                List<DebugFrame> frames = debugger.pdbgShowFrames();
                System.out.println("栈帧数量: " + frames.size());
            }

        } finally {
            // 终止调试
            debugger.pdbgAbort();
            debugger.close();
        }
    }

    private String getTypeName(int typeCode) {
        // java.sql.Types中的常见类型
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

            System.out.println("=== 示例1: 简单调试流程 ===");
            example.simpleDebugFlow(conn);

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

