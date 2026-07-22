## Description

`javax.transaction.xa.Xid` is the data model that defines the unique identifier for XA transactions. It serves as the "ID card" for global transactions (overall transactions across multiple resources) and local transaction branches (sub-transactions on a single resource). An `Xid` consists of three parts: "format ID + global transaction ID + branch transaction ID". The transaction manager can precisely identify "which resource branch under which global transaction" through the `Xid`, thereby sending unified commit and rollback commands to different resource's `XAResource`, avoiding transaction branch confusion and ensuring consistency of multi-resource transactions.



Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.transaction.xa/javax/transaction/xa/Xid.html).

## Method

The YashanDB JDBC driver has the following methods for the Xid interface:

|Method |Return Type |Notes |
|-----------------------------------|--------|--------------------|
| getFormatId()                     | int    | -                   |
| getGlobalTransactionId()            | byte[]      | According to XA specification, this field has a maximum length of 64 bits |
| getBranchQualifier(String value)    | byte[]      | According to XA specification, this field has a maximum length of 64 bits |

The Xid implementation class of the YashanDB JDBC driver is YasXid, which provides the constructor YasXid(int formatId, byte[] gtrid, byte[] bqual) throws XAException.

> **Note**: 
>
> The Xid interface is a standard interface defined by the JDBC specification. Users can use the YasXid implementation provided by the YashanDB JDBC driver or use other implementations (as long as they correctly implement the three methods in the Xid interface).

In YashanDB, the formatId field of Xid will be ignored, and the trailing 0s in the gtrid and bqual fields will be ignored, meaning that {97, 97, 97}, {97, 97, 97, 0}, and {97, 97, 97, 0, 0, 0, 0, 0} are equivalent when used as gtrid or bqual.

## Example

```java
package quickstart;

import javax.transaction.xa.Xid;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

// Custom implementation of Xid interface
class MyXid implements Xid {
    private final int formatId;
    private final byte[] gtrid;
    private final byte[] bqual;

    public MyXid(int formatId, String gtrid, String bqual) {
        this.formatId = formatId;
        this.gtrid = gtrid.getBytes();
        this.bqual = bqual.getBytes();
    }

    @Override
    public int getFormatId() {
        return formatId;
    }

    @Override
    public byte[] getGlobalTransactionId() {
        return gtrid;
    }

    @Override
    public byte[] getBranchQualifier() {
        return bqual;
    }
}

public class CompleteXidExample {

    public static void main(String[] args) {
        CompleteXidExample example = new CompleteXidExample();

        // 1. Demonstrate basic Xid functionality
        example.demonstrateXidMethods();

        // 2. Business scenario example
        example.ecommerceOrderScenario();

        // 3. Distributed transaction example
        example.distributedBankingScenario();
    }

    public void demonstrateXidMethods() {
        System.out.println("=== Demonstrating Xid Interface Methods ===\n");

        // Create Xid
        Xid xid = new MyXid(0, "DEMO_TX_001", "TEST_BRANCH");

        // 1. Get format ID
        int formatId = xid.getFormatId();
        System.out.println("1. Format ID: " + formatId);
        System.out.println("   Description: " + getFormatDescription(formatId));

        // 2. Get global transaction ID
        byte[] gtridBytes = xid.getGlobalTransactionId();
        String gtrid = new String(gtridBytes);
        System.out.println("\n2. Global Transaction ID:");
        System.out.println("   String: " + gtrid);
        System.out.println("   Byte length: " + gtridBytes.length);
        System.out.println("   Is valid: " + isValidGlobalTransactionId(gtrid));

        // 3. Get branch qualifier
        byte[] bqualBytes = xid.getBranchQualifier();
        String bqual = new String(bqualBytes);
        System.out.println("\n3. Branch Qualifier:");
        System.out.println("   String: " + bqual);
        System.out.println("   Byte length: " + bqualBytes.length);
        System.out.println("   Is valid: " + validateBranchQualifier(bqual));

        // 4. Complete Xid information
        System.out.println("\n4. Complete Xid Information:");
        System.out.println("   " + xidToString(xid));
    }

    public void ecommerceOrderScenario() {
        System.out.println("\n\n=== E-commerce Order Processing Scenario ===\n");

        // Create order
        long orderId = 1001;
        String customerId = "CUST001";

        // Generate global transaction ID
        String globalTxId = generateOrderTransactionId(orderId, customerId);
        System.out.println("Global Transaction ID: " + globalTxId);

        // Create transaction branches
        Xid[] branches = {
                new MyXid(0, globalTxId, "ORDER_CREATION"),
                new MyXid(0, globalTxId, "INVENTORY_CHECK"),
                new MyXid(0, globalTxId, "PAYMENT_PROCESSING"),
                new MyXid(0, globalTxId, "SHIPPING_ARRANGEMENT")
        };

        // Process each branch
        for (Xid branch : branches) {
            processOrderBranch(branch);
        }

        // Simulate transaction commit
        simulateTransactionCommit(branches);
    }
    

    public void distributedBankingScenario() {
        System.out.println("\n\n=== Distributed Banking Transaction Scenario ===\n");

        // Bank transfer scenario
        String fromAccount = "ACC001";
        String toAccount = "ACC002";
        double amount = 1000.00;

        // Generate global transaction ID
        String globalTxId = generateTransferTransactionId(fromAccount, toAccount, amount);
        System.out.println("Transfer Transaction ID: " + globalTxId);

        // Create resource branches
        Map<String, Xid> resourceBranches = new HashMap<>();

        // Source account branch
        resourceBranches.put("SOURCE_DB",
                new MyXid(0, globalTxId, "DB_ORACLE_SOURCE"));

        // Target account branch
        resourceBranches.put("TARGET_DB",
                new MyXid(0, globalTxId, "DB_ORACLE_TARGET"));

        // Audit log branch
        resourceBranches.put("AUDIT_LOG",
                new MyXid(0, globalTxId, "AUDIT_SYSTEM"));

        // Notification branch
        resourceBranches.put("NOTIFICATION",
                new MyXid(0, globalTxId, "MQ_NOTIFICATION"));

        // Process distributed transaction
        processDistributedTransfer(resourceBranches, fromAccount, toAccount, amount);
    }

    // Helper methods
    private String getFormatDescription(int formatId) {
        switch (formatId) {
            case 0:
                return "Standard X/Open format";
            case 1:
                return "Oracle format";
            case 2:
                return "IBM DB2 format";
            default:
                return "Custom format (" + formatId + ")";
        }
    }

    private boolean isValidGlobalTransactionId(String gtrid) {
        return gtrid != null && gtrid.length() > 0 && gtrid.length() <= 64;
    }

    private boolean validateBranchQualifier(String bqual) {
        return bqual != null && bqual.length() > 0 && bqual.length() <= 32;
    }

    private String xidToString(Xid xid) {
        return String.format("Xid[format=%d, gtrid=%s, bqual=%s]",
                xid.getFormatId(),
                new String(xid.getGlobalTransactionId()),
                new String(xid.getBranchQualifier()));
    }

    private String generateOrderTransactionId(long orderId, String customerId) {
        String timestamp = new SimpleDateFormat("yyyyMMddHHmmss")
                .format(new Date());
        return String.format("ORDER_%d_%s_%s", orderId, customerId, timestamp);
    }

    private String generateTransferTransactionId(String from, String to, double amount) {
        String timestamp = String.valueOf(System.currentTimeMillis());
        return String.format("XFER_%s_%s_%.2f_%s", from, to, amount, timestamp);
    }

    private void processOrderBranch(Xid branch) {
        String bqual = new String(branch.getBranchQualifier());
        System.out.println("Processing branch: " + bqual);

        switch (bqual) {
            case "ORDER_CREATION":
                System.out.println("  Creating order record...");
                break;
            case "INVENTORY_CHECK":
                System.out.println("  Checking inventory...");
                break;
            case "PAYMENT_PROCESSING":
                System.out.println("  Processing payment...");
                break;
            case "SHIPPING_ARRANGEMENT":
                System.out.println("  Arranging shipping...");
                break;
        }
    }

    private void simulateTransactionCommit(Xid[] branches) {
        System.out.println("\nSimulating transaction commit...");
        for (Xid branch : branches) {
            System.out.println("Committing branch: " +
                    new String(branch.getBranchQualifier()));
        }
        System.out.println("Transaction committed successfully!");
    }

    private void processDistributedTransfer(Map<String, Xid> branches,
                                            String fromAccount,
                                            String toAccount,
                                            double amount) {
        System.out.println("Processing distributed transfer...");
        System.out.println("Amount: " + amount);
        System.out.println("From: " + fromAccount);
        System.out.println("To: " + toAccount);

        // Two-phase commit simulation
        System.out.println("\n1. Prepare phase:");
        for (Map.Entry<String, Xid> entry : branches.entrySet()) {
            System.out.println("  Preparing " + entry.getKey() + "...");
        }

        System.out.println("\n2. Commit phase:");
        for (Map.Entry<String, Xid> entry : branches.entrySet()) {
            System.out.println("  Committing " + entry.getKey() + "...");
        }

        System.out.println("\nTransfer completed successfully!");
    }
}
```

