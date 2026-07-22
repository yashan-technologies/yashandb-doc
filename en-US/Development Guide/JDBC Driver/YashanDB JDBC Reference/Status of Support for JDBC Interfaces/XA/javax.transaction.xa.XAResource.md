## Description

The XAResource interface represents that it is used for XA operations.

`javax.transaction.xa.XAResource` represents that it acts as a transaction proxy for each resource (such as YashanDB and Oracle databases) in a distributed transaction. The transaction manager does not need to care about the specific implementation of the underlying resources; it only needs to send instructions such as prepare to commit, confirm commit, and rollback to each resource through the `XAResource` interface, thus achieving unified coordination of multi-resource transactions.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.transaction.xa/javax/transaction/xa/XAResource.html).

## Method

The YashanDB JDBC driver has the following methods for the XAResource interface:

|Method |Return Type |Notes |
|----------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| start(Xid xid, int flags) throws XAException | void        | The flag supports the following values:<br/>XAResource.TMNOFLAGS = 0;<br/>XAResource.TMJOIN=2097152;<br/>XAResource.TMRESUME=134217728; |
| end(Xid xid, int flags) throws XAException   | void        | The flag supports the following values:<br/>XAResource.TMSUSPEND = 33554432;<br/>XAResource.TMSUCCESS = 67108864;<br/>XAResource.TMFAIL = 536870912; |
| prepare(Xid xid) throws XAException          | int      |  -                                                                                                                        |
| commit(Xid xid, boolean onePhase)            | void     |      -                                                                                                                    |
| rollback(Xid xid) throws XAException         | void     |  -                                                                                                                        |
| forget(Xid xid) throws XAException           | void     |   -                                                                                                                       |
| recover(int flag) throws XAException         | Xid[]       | Queries all current Xid; the parameter flag will be ignored                                                             |
| getTransactionTimeout()                      | int         | Gets the configured transaction timeout                                                                                 |
| setTransactionTimeout(int i)                 | boolean     | Sets the transaction timeout (timeout mechanism is not yet implemented)                                                  |

## Example

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

    // Create database connection
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

    // Get XAResource
    public XAResource getXAResource() throws SQLException {
        XADataSource xaDS = new com.yashandb.xa.YasXADataSource();
        ((com.yashandb.xa.YasXADataSource) xaDS).setURL("jdbc:yasdb://192.168.1.2:1688/yasdb");
        XAConnection xaConn = xaDS.getXAConnection("sales", "sales");
        return xaConn.getXAResource();
    }

    public void completeXATransactionFlow(XAResource xaResource) throws XAException {
        Xid xid = createXid(1, "COMPLETE_TXN_" + System.currentTimeMillis(), "MAIN_BRANCH");

        try {
            // 1. Set timeout
            xaResource.setTransactionTimeout(30);
            System.out.println("Transaction timeout set to 30 seconds");

            // 2. Start transaction
            xaResource.start(xid, XAResource.TMNOFLAGS);
            System.out.println("Transaction started: " + xidToString(xid));

            // 3. Execute business operations
            executeBusinessOperations();

            // 4. Successfully end transaction
            xaResource.end(xid, XAResource.TMSUCCESS);
            System.out.println("Transaction operations completed successfully");

            // 5. Prepare phase
            int prepareResult = xaResource.prepare(xid);
            System.out.println("Prepare result: " + prepareResultToString(prepareResult));

            if (prepareResult == XAResource.XA_OK) {
                // 6. Commit transaction
                xaResource.commit(xid, false);
                System.out.println("Transaction committed successfully");

            } else if (prepareResult == XAResource.XA_RDONLY) {
                // Read-only transaction, one-phase commit
                xaResource.commit(xid, true);
                System.out.println("Read-only transaction committed");

            } else {
                // Prepare failed, rollback
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

        // 1. Check current timeout settings
        int timeout = xaResource.getTransactionTimeout();
        System.out.println("Current transaction timeout: " + timeout + " seconds");

        // 2. Recover transactions that need processing
        Xid[] recoveredXids = xaResource.recover(XAResource.TMSTARTRSCAN | XAResource.TMENDRSCAN);

        if (recoveredXids != null && recoveredXids.length > 0) {
            System.out.println("Found " + recoveredXids.length + " transactions to recover");

            for (Xid xid : recoveredXids) {
                System.out.println("\nProcessing: " + xidToString(xid));

                try {
                    // Try to determine transaction state
                    int state = getTransactionState(xaResource, xid);

                    switch (state) {
                        case XAResource.XA_PREPARED:
                            // Prepared transaction, need to decide commit or rollback
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
                            // Heuristically completed transaction, can be forgotten
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
        // Multi-phase complex transaction
        Xid mainXid = createXid(1, "MULTI_PHASE_TXN", "MAIN");
        Xid suspendXid = createXid(1, "SUSPENDED_TXN", "SUSPEND");

        // Phase 1: Start main transaction
        xaResource.setTransactionTimeout(60);
        xaResource.start(mainXid, XAResource.TMNOFLAGS);
        executePhase1();

        // Phase 2: Suspend main transaction, execute another transaction
        xaResource.end(mainXid, XAResource.TMSUSPEND);

        xaResource.setTransactionTimeout(10);
        xaResource.start(suspendXid, XAResource.TMNOFLAGS);
        executeSuspendedOperation();
        xaResource.end(suspendXid, XAResource.TMSUCCESS);
        xaResource.commit(suspendXid, true);

        // Phase 3: Resume main transaction
        xaResource.start(mainXid, XAResource.TMRESUME);
        executePhase2();

        // Complete main transaction
        xaResource.end(mainXid, XAResource.TMSUCCESS);

        if (xaResource.prepare(mainXid) == XAResource.XA_OK) {
            xaResource.commit(mainXid, false);
        }
    }

    // Helper methods
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
        // Execute business operations
    }

    private void executePhase1() {
        // Execute phase 1 operations
    }

    private void executePhase2() {
        // Execute phase 2 operations
    }

    private void executeSuspendedOperation() {
        // Execute suspended operation
    }

    private int getTransactionState(XAResource xaResource, Xid xid) {
        // Return transaction state
        return XAResource.XA_PREPARED;
    }

    private boolean shouldCommitPreparedTransaction(Xid xid) {
        // Determine whether to commit or rollback
        return true;
    }
}
```

