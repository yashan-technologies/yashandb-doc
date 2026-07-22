## 描述

`DebugBreakpoint`是调试器触发类核心函数，作用是主动在代码执行流程中插入调试断点。当程序运行到该函数调用处时，会暂停执行并触发调试器的中断，让开发者可以查看此时的变量值、调用栈、内存状态等关键信息，是手动控制调试流程的重要工具。


## 方法

描述断点信息的DebugBreakpoint接口有如下方法：

| 方法| 返回类型| 备注|
| ----------------- | ------------------------ | ------------------------------------------------------------ |
| getId()           | int                      | 获取断点的全局id                                             |
| getObjectId()     | long                     | 获取断点所在存储过程的objectId                               |
| getSubprogramId() | int                      | 获取断点所在存储过程的subprogramId                           |
| getLineNum()      | int                      | 获取断点所在存储过程的行号                                   |
| setId(int id)     | void                     | 设置断点的全局id，一般由系统返回不需要设置                   |

## 示例

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

    // 完整的断点调试示例
    public void debugStoredProcedure(Connection conn) throws SQLException {
        // 1. 创建可调试的CallableStatement
        YasDebugCallableStatement stmt = (YasDebugCallableStatement)
            conn.prepareCall("{call CALCULATE_TAX(?, ?)}");

        // 2. 获取Debugger接口
        Debugger debugger = stmt;

        // 在添加断点前设置输入参数
        stmt.setDouble(1, 75000.0);
        stmt.registerOutParameter(2, Types.DOUBLE);

        try {
            // 3. 添加断点（需要objectId、subprogramId、lineNum）
            // 需要先从DBA_PROCEDURES查询objectId和subprogramId
            // 示例：SELECT OBJECT_ID, SUBPROGRAM_ID FROM DBA_PROCEDURES WHERE OWNER = 'USER' AND OBJECT_NAME = 'CALCULATE_TAX'
            long objectId = 12345;  // 替换为数据库中的实际OBJECT_ID
            int subprogramId = 0;   // 通常顶级存储过程为0
            DebugBreakpoint bp1 = debugger.pdbgAddBreakpoint(objectId, subprogramId, 10);
            DebugBreakpoint bp2 = debugger.pdbgAddBreakpoint(objectId, subprogramId, 25);

            // 4. 获取断点信息
            System.out.println("Breakpoint 1 information:");
            System.out.println("  ID: " + bp1.getId());
            System.out.println("  Object ID: " + bp1.getObjectId());
            System.out.println("  Subprogram ID: " + bp1.getSubprogramId());
            System.out.println("  Line number: " + bp1.getLineNum());

            // 5. 开始调试 - 将会执行并在断点处停止
            debugger.pdbgStart();
            // 6. 检查调试模式是否开启
            if (debugger.isDebugOn()) {
                System.out.println("Debug mode is active");
                System.out.println("  Running at objectId: " + debugger.getRunObjectId());
                System.out.println("  Running at subprogramId: " + debugger.getRunSubprogramId());
                System.out.println("  Running at lineNum: " + debugger.getRunLineNum());

                // 7. 在断点处检查变量
                List<DebugVar> vars = debugger.pdbgShowFrameVariables();
                System.out.println("Variables at breakpoint:");
                for (DebugVar var : vars) {
                    System.out.println("  " + var.getName() + " = " + var.getVar());
                }

                // 8. 显示堆栈帧
                List<DebugFrame> frames = debugger.pdbgShowFrames();
                System.out.println("Stack frames:");
                for (DebugFrame frame : frames) {
                    System.out.println("  Frame: " + frame);
                }

                // 9. 继续执行（逐步执行）
                // 步入
                // debugger.pdbgStepInto();

                // 步出
                // debugger.pdbgStepOut();

                // 步过
                // debugger.pdbgStepNext();

                // 继续执行到下一个断点或结束
                debugger.pdbgContinue();
            }

            // 10. 执行后获取输出参数
            double tax = stmt.getDouble(2);
            System.out.println("Tax calculated: " + tax);

        } finally {
            // 11. 清理断点
            debugger.pdbgDeleteAllBreakpoints();
            stmt.close();
        }
    }

    // 示例：如何从数据库获取objectId和subprogramId
    public void getProcedureInfo(Connection conn) throws SQLException {
        String procName = "CALCULATE_TAX";

        // 查询存储过程信息
        java.sql.PreparedStatement ps = conn.prepareStatement(
            "SELECT OBJECT_ID, SUBPROGRAM_ID FROM DBA_PROCEDURES " +
            "WHERE OWNER = ? AND OBJECT_NAME = ?"
        );
        ps.setString(1, "SALES");  // 所有者
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
