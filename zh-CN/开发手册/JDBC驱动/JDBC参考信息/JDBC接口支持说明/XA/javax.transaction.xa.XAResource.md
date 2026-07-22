## 描述

XAResource接口用于进行XA操作。

`javax.transaction.xa.XAResource`就像分布式事务中每个资源的事务代理，事务管理器无需关心底层资源的具体实现，只需通过`XAResource`接口向各个资源发送准备提交、确认提交、回滚等指令，从而实现多资源事务的统一协调。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.transaction.xa/javax/transaction/xa/XAResource.html)。

## 方法

YashanDB JDBC驱动对XAResource接口有如下方法：

| 方法|  返回类型| 备注|
|----------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| start(Xid xid, int flags) throws XAException | void     | flag支持如下取值：<br/>XAResource.TMNOFLAGS = 0;<br/>XAResource.TMJOIN=2097152;<br/>XAResource.TMRESUME=134217728;              |
| end(Xid xid, int flags) throws XAException   | void     | flag支持如下取值：<br/>XAResource.TMSUSPEND = 33554432;<br/>XAResource.TMSUCCESS = 67108864;<br/>XAResource.TMFAIL = 536870912; |
| prepare(Xid xid) throws XAException          | int      |  -                                                                                                                        |
| commit(Xid xid, boolean onePhase)            | void     |      -                                                                                                                    |
| rollback(Xid xid) throws XAException         | void     |  -                                                                                                                        |
| forget(Xid xid) throws XAException           | void     |   -                                                                                                                       |
| recover(int flag) throws XAException         | Xid[]    | 查询当前所有的Xid，参数flag会被忽略                                                                                                   |
| getTransactionTimeout()                      | int      | 获取设置的事务超时时间                                                                                                             |
| setTransactionTimeout(int i)                 | boolean  | 设置事务超时时间（超时机制暂未实现）                                                                                                     |

## 示例

```java
package quickstart;

import javax.sql.XAConnection;
import javax.sql.XADataSource;
import javax.transaction.xa.XAResource;
import javax.transaction.xa.XAException;
import javax.transaction.xa.Xid;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class XAResourceCompleteExample {

    // 创建数据库连接
    public static Connection getConnection() throws SQLException {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        try {
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        return DriverManager.getConnection(sourceURL, user, password);
    }

    // 获取 XAResource
    public XAResource getXAResource() throws SQLException {
        XADataSource xaDS = new com.yashandb.xa.YasXADataSource();
        ((com.yashandb.xa.YasXADataSource) xaDS).setURL("jdbc:yasdb://192.168.1.2:1688/yasdb");
        XAConnection xaConn = xaDS.getXAConnection("sales", "sales");
        return xaConn.getXAResource();
    }

    public void completeXATransactionFlow(XAResource xaResource) throws XAException {
        Xid xid = createXid(1, "COMPLETE_TXN_" + System.currentTimeMillis(), "MAIN_BRANCH");

        try {
            // 1. 设置事务超时时间
            xaResource.setTransactionTimeout(30);
            System.out.println("Transaction timeout set to 30 seconds");

            // 2. 开启事务
            xaResource.start(xid, XAResource.TMNOFLAGS);
            System.out.println("Transaction started: " + xidToString(xid));

            // 3. 执行业务操作
            executeBusinessOperations();

            // 4. 正常结束事务操作
            xaResource.end(xid, XAResource.TMSUCCESS);
            System.out.println("Transaction operations completed successfully");

            // 5. 提交准备阶段
            int prepareResult = xaResource.prepare(xid);
            System.out.println("Prepare result: " + prepareResultToString(prepareResult));

            if (prepareResult == XAResource.XA_OK) {
                // 6. 提交事务
                xaResource.commit(xid, false);
                System.out.println("Transaction committed successfully");

            } else if (prepareResult == XAResource.XA_RDONLY) {
                // 只读事务，一阶段提交
                xaResource.commit(xid, true);
                System.out.println("Read-only transaction committed");

            } else {
                // 预提交失败，回滚
                xaResource.rollback(xid);
                System.out.println("Transaction rolled back due to prepare failure");
            }

        } catch (Exception e) {
            System.err.println("Transaction failed: " + e.getMessage());
            try {
                xaResource.rollback(xid);
                System.out.println("Transaction rolled back due to exception");
            } catch (XAException rollbackEx) {
                System.err.println("Rollback failed: " + rollbackEx.getMessage());
            }
            throw e;
        }
    }
    

    public void recoveryManagement(XAResource xaResource) throws XAException {
        System.out.println("\n=== Transaction Recovery Management ===");

        // 1. 查看当前超时配置
        int timeout = xaResource.getTransactionTimeout();
        System.out.println("Current transaction timeout: " + timeout + " seconds");

        // 2. 恢复待处理事务
        Xid[] recoveredXids = xaResource.recover(XAResource.TMSTARTRSCAN | XAResource.TMENDRSCAN);

        if (recoveredXids != null && recoveredXids.length > 0) {
            System.out.println("Found " + recoveredXids.length + " transactions to recover");

            for (Xid xid : recoveredXids) {
                System.out.println("\nProcessing: " + xidToString(xid));

                try {
                    // 尝试确定事务状态
                    int state = getTransactionState(xaResource, xid);

                    switch (state) {
                        case XAResource.XA_PREPARED:
                            // 已预提交的事务，需要决定提交或回滚
                            boolean shouldCommit = shouldCommitPreparedTransaction(xid);
                            if (shouldCommit) {
                                xaResource.commit(xid, false);
                                System.out.println("Committed prepared transaction");
                            } else {
                                xaResource.rollback(xid);
                                System.out.println("Rolled back prepared transaction");
                            }
                            break;
                            

                        case XAResource.XA_HEURCOM:
                        case XAResource.XA_HEURRB:
                            // 启发式完成的事务，可以忘记
                            xaResource.forget(xid);
                            System.out.println("Forgot heuristic transaction");
                            break;

                        default:
                            System.out.println("Transaction in unexpected state: " + state);
                    }

                } catch (XAException e) {
                    System.err.println("Failed to process transaction: " + e.getMessage());
                }
            }
        } else {
            System.out.println("No transactions to recover");
        }
    }

    public void multiPhaseTransaction(XAResource xaResource) throws XAException {
        // 多阶段复杂事务
        Xid mainXid = createXid(1, "MULTI_PHASE_TXN", "MAIN");
        Xid suspendXid = createXid(1, "SUSPENDED_TXN", "SUSPEND");

        // 阶段 1：启动主事务
        xaResource.setTransactionTimeout(60);
        xaResource.start(mainXid, XAResource.TMNOFLAGS);
        executePhase1();

        // 阶段 2：暂停主事务，执行另一个事务
        xaResource.end(mainXid, XAResource.TMSUSPEND);

        xaResource.setTransactionTimeout(10);
        xaResource.start(suspendXid, XAResource.TMNOFLAGS);
        executeSuspendedOperation();
        xaResource.end(suspendXid, XAResource.TMSUCCESS);
        xaResource.commit(suspendXid, true);

        // 阶段 3：恢复主事务
        xaResource.start(mainXid, XAResource.TMRESUME);
        executePhase2();

        // 完成主事务
        xaResource.end(mainXid, XAResource.TMSUCCESS);

        if (xaResource.prepare(mainXid) == XAResource.XA_OK) {
            xaResource.commit(mainXid, false);
        }
    }

    // 辅助方法
    private String xidToString(Xid xid) {
        return "Xid[format=" + xid.getFormatId() +
               ", gtrid=" + new String(xid.getGlobalTransactionId()) +
               ", bqual=" + new String(xid.getBranchQualifier()) + "]";
    }

    private String prepareResultToString(int result) {
        switch (result) {
            case XAResource.XA_OK: return "XA_OK";
            case XAResource.XA_RDONLY: return "XA_RDONLY";
            case XAException.XA_RBROLLBACK: return "XA_RBROLLBACK";
            case XAException.XA_RBDEADLOCK: return "XA_RBDEADLOCK";
            case XAException.XA_RBTIMEOUT: return "XA_RBTIMEOUT";
            default: return "Unknown(" + result + ")";
        }
    }

    private Xid createXid(int formatId, String globalTxId, String branchQualifier) {
        return new Xid() {
            @Override
            public int getFormatId() { return formatId; }
            @Override
            public byte[] getGlobalTransactionId() { return globalTxId.getBytes(); }
            @Override
            public byte[] getBranchQualifier() { return branchQualifier.getBytes(); }
        };
    }

    private void executeBusinessOperations() {
        // 执行业务操作
    }

    private void executePhase1() {
        // 执行阶段1操作
    }

    private void executePhase2() {
        // 执行阶段2操作
    }

    private void executeSuspendedOperation() {
        // 执行暂停的操作
    }

    private int getTransactionState(XAResource xaResource, Xid xid) {
        // 返回事务状态
        return XAResource.XA_PREPARED;
    }

    private boolean shouldCommitPreparedTransaction(Xid xid) {
        // 判断是提交还是回滚
        return true;
    }
}
```

