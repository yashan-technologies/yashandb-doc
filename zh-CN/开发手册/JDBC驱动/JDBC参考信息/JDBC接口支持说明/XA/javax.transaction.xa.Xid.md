## 描述

`javax.transaction.xa.Xid`是用户定义XA事务的唯一标识的数据模型，它是全局事务（跨多个资源的整体事务）和本地事务分支（单个资源上的子事务）的“身份证号”，一个`Xid`包含「格式ID + 全局事务ID + 分支事务ID」三部分，事务管理器通过`Xid`能精准识别 "哪个全局事务下的哪个资源分支"，从而向不同资源的`XAResource`发送统一的提交、回滚指令，避免事务分支混乱，保证多资源事务的一致性。



详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.transaction.xa/javax/transaction/xa/Xid.html)。

## 方法

YashanDB JDBC驱动对Xid接口有如下方法：

| 方法|  返回类型| 备注|
|-----------------------------------|--------|--------------------|
| getFormatId()                     | int    | -                   |
| getGlobalTransactionId()          | byte[] | 按照XA规范，该字段长度最大为64位 |
| getBranchQualifier(String value)  | byte[] | 按照XA规范，该字段长度最大为64位 |

YashanDB JDBC驱动的Xid实现类为YasXid，提供YasXid(int formatId, byte[] gtrid, byte[] bqual) throws XAException构造方法。

> **Note**: 
>
> Xid接口是JDBC规范定义的标准接口，用户可以使用YashanDB JDBC驱动提供的YasXid实现，也可以使用其他实现（只需正确实现了Xid接口中的三个方法即可）。

在YashanDB中，Xid的formatId字段会被忽略，gtrid和bqual字段尾部的0会被忽略，即{97, 97, 97}、{97, 97, 97, 0}与{97, 97, 97, 0, 0, 0, 0, 0}在作为gtrid或bqual时是等价的。

## 示例

```java
package quickstart;

import javax.transaction.xa.Xid;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

// Xid接口的自定义实现
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

        // 1. 演示Xid基本功能
        example.demonstrateXidMethods();

        // 2. 业务场景示例
        example.ecommerceOrderScenario();

        // 3. 分布式事务示例
        example.distributedBankingScenario();
    }

    public void demonstrateXidMethods() {
        System.out.println("=== Demonstrating Xid Interface Methods ===\n");

        // 创建Xid
        Xid xid = new MyXid(0, "DEMO_TX_001", "TEST_BRANCH");

        // 1. 获取格式ID
        int formatId = xid.getFormatId();
        System.out.println("1. Format ID: " + formatId);
        System.out.println("   Description: " + getFormatDescription(formatId));

        // 2. 获取全局事务ID
        byte[] gtridBytes = xid.getGlobalTransactionId();
        String gtrid = new String(gtridBytes);
        System.out.println("\n2. Global Transaction ID:");
        System.out.println("   String: " + gtrid);
        System.out.println("   Byte length: " + gtridBytes.length);
        System.out.println("   Is valid: " + isValidGlobalTransactionId(gtrid));

        // 3. 获取分支限定符
        byte[] bqualBytes = xid.getBranchQualifier();
        String bqual = new String(bqualBytes);
        System.out.println("\n3. Branch Qualifier:");
        System.out.println("   String: " + bqual);
        System.out.println("   Byte length: " + bqualBytes.length);
        System.out.println("   Is valid: " + validateBranchQualifier(bqual));

        // 4. 完整Xid信息
        System.out.println("\n4. Complete Xid Information:");
        System.out.println("   " + xidToString(xid));
    }

    public void ecommerceOrderScenario() {
        System.out.println("\n\n=== E-commerce Order Processing Scenario ===\n");

        // 创建订单
        long orderId = 1001;
        String customerId = "CUST001";

        // 生成全局事务ID
        String globalTxId = generateOrderTransactionId(orderId, customerId);
        System.out.println("Global Transaction ID: " + globalTxId);

        // 创建事务分支
        Xid[] branches = {
                new MyXid(0, globalTxId, "ORDER_CREATION"),
                new MyXid(0, globalTxId, "INVENTORY_CHECK"),
                new MyXid(0, globalTxId, "PAYMENT_PROCESSING"),
                new MyXid(0, globalTxId, "SHIPPING_ARRANGEMENT")
        };

        // 处理每个分支
        for (Xid branch : branches) {
            processOrderBranch(branch);
        }

        // 模拟事务提交
        simulateTransactionCommit(branches);
    }
    

    public void distributedBankingScenario() {
        System.out.println("\n\n=== Distributed Banking Transaction Scenario ===\n");

        // 银行转账场景
        String fromAccount = "ACC001";
        String toAccount = "ACC002";
        double amount = 1000.00;

        // 生成全局事务ID
        String globalTxId = generateTransferTransactionId(fromAccount, toAccount, amount);
        System.out.println("Transfer Transaction ID: " + globalTxId);

        // 创建资源分支
        Map<String, Xid> resourceBranches = new HashMap<>();

        // 源账户分支
        resourceBranches.put("SOURCE_DB",
                new MyXid(0, globalTxId, "DB_ORACLE_SOURCE"));

        // 目标账户分支
        resourceBranches.put("TARGET_DB",
                new MyXid(0, globalTxId, "DB_ORACLE_TARGET"));

        // 审计日志分支
        resourceBranches.put("AUDIT_LOG",
                new MyXid(0, globalTxId, "AUDIT_SYSTEM"));

        // 消息通知分支
        resourceBranches.put("NOTIFICATION",
                new MyXid(0, globalTxId, "MQ_NOTIFICATION"));

        // 处理分布式事务
        processDistributedTransfer(resourceBranches, fromAccount, toAccount, amount);
    }

    // 辅助方法
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

        // 两阶段提交模拟
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

