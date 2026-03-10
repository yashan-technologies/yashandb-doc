A Restore Point acts like a bookmark, marking a specific SCN or timestamp to facilitate locating target points for database flashback operations.

Based on persistence, restore points are classified into Permanent Restore Points and Normal Restore Points: 

| Features  | Permanent Restore Points | Normal Restore Points |
|--------------------|-------------------|-------------------|
| Flashback Log Dependency | Force retention of flashback logs after the permanent restore point | Flashback log files may be automatically cleaned; normal restore points become invalid after associated logs are removed |
| Storage Overhead | High<br/>Flashback logs must be retained permanently  | Low<br/>Only timestamps or SCNs are recorded |
| Deletion Mechanism | Manual deletion only | * Manual deletion allowed<br/>Some restore points are deleted when flashback log files are removed, including: when storage space for flashback log files becomes insufficient, flashback log files are automatically cleaned up; after flashback database operations, flashback log files older than the target time point are automatically cleaned up.<br />* All normal restore points are automatically deleted when Flashback is disabled |
| Impact of recovery operations on restore points | * For [backup and recovery database](../../备份恢复/00备份恢复): After recovery, all restore points will be automatically deleted <br/>* For [flashback database](./闪回恢复数据库): After a full-database flashback, any restore point created after the target recovery time point will be automatically deleted | Same as permanent restore point |
| Additional Notes | * Mandatory retention of [archive log files](../../基本数据库管理/归档管理.html#auto_cleanup) <br/>* Normally cannot [disable database flashback](./全库闪回基础配置.html#flashback_off) when permanent restore points exist, but flashback on old primary is forcibly and automatically disabled during SWITCHOVER (even if permanent restore points exist) | - |


##  Creating Restore Points

Before performing high-risk operations, you can create a restore point to mark a known good state and select either a permanent or normal restore point based on recovery requirements.

###  Prerequisites

- [Database flashback](./全库闪回基础配置) must be enabled.

- The instance must be in MOUNT or OPEN state.

- User privileges:

    - Permanent Restore Points: Requires SYSDBA privileges.

    - Normal Restore Points: Requires FLASHBACK ANY TABLE privilege or higher.

- The restore point's SCN must be later than the OLDEST_FLASHBACK_SCN value in [V$FLASHBACK_DATABASE_LOG](../../../参考手册/系统视图/动态视图/V$FLASHBACK_DATABASE_LOG).

###  Operation Steps

::: tabs
== Permanent Restore Points

1. Connect and log in to the database with a user that meets the required permissions.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Create permanent restore points using [CREATE RESTORE POINT](../../../开发手册/SQL参考手册/SQL语句/CREATE RESTORE POINT):

    ```sql
    -- Method 1: Create via SCN
    -- 1. Obtain SCN information (this example uses the current SCN)
    SELECT CURRENT_SCN FROM V$DATABASE;
    
              CURRENT_SCN
    ---------------------
       725539138356854784
    
    -- 2. Create a permanent restore point
    CREATE RESTORE POINT G20241201_01 AS OF SCN 725539138356854784 GUARANTEE FLASHBACK DATABASE;
    
    -- Method 2: Create via timestamp
    CREATE RESTORE POINT G20241201_02 AS OF TIMESTAMP SYSTIMESTAMP() GUARANTEE FLASHBACK DATABASE;
    
    -- Method 3: Create at current time 
    CREATE RESTORE POINT G202412011100 GUARANTEE FLASHBACK DATABASE;
    ```

== Normal Restore Points

1. Connect and log in to the database with a user that meets the required permissions.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Create normal restore points using [CREATE RESTORE POINT](../../../开发手册/SQL参考手册/SQL语句/CREATE RESTORE POINT):

    ```sql
    -- Method 1: Create via SCN
    -- 1. Obtain SCN information (this example uses the current SCN)
    SELECT CURRENT_SCN FROM V$DATABASE;

              CURRENT_SCN
    ---------------------
       725539138356854784

    -- 2. Create a normal restore point 
    CREATE RESTORE POINT N20241201_01 AS OF SCN 725539138356854784;

    -- Method 2: Create via timestamp
    CREATE RESTORE POINT N20241201_02 AS OF TIMESTAMP SYSTIMESTAMP();

    -- Method 3: Create at current time
    CREATE RESTORE POINT N202412011100;
    ```
:::

##  Viewing Existing Restore Points

The [GV$RESTORE_POINT](../../../参考手册/系统视图/动态视图/GV$RESTORE_POINT) and [V$RESTORE_POINT](../../../参考手册/系统视图/动态视图/V$RESTORE_POINT) views contain information about restore points. Before performing a full database Flashback, you can first query these views to identify the target time point for recovery.

```sql
SELECT NAME,SCN,IS_GUARANTEE FROM V$RESTORE_POINT;

NAME                                                                               SCN IS_GUARANTEE
---------------------------------------------------------------- --------------------- ------------
G20241201_01                                                        725539138356854784            1
G20241201_02                                                        725542053510234112            1
G202412011100                                                       725539138356854784            1
N20241201_01                                                        725539138356854784            0
N20241201_02                                                        725542222390407168            0
N202412011100                                                       725539138356854784            0
```

- `IS_GUARANTEE = 1` indicates that the restore point is a permanent restore point.

- `IS_GUARANTEE = 0` indicates that the restore point is a normal restore point.

##  Deleting Restore Points

When a restore point is no longer needed, delete it to avoid clutter and interference during database flashback operations.

For permanent restore points, regular manual deletion should be performed to prevent space exhaustion caused by log file accumulation.

To [disable database flashback](./全库闪回基础配置), all permanent restore points must be manually deleted first.

###  Prerequisites

- [Database flashback](./全库闪回基础配置) must be enabled.

- The instance must be in MOUNT or OPEN state.

- User privileges:

    - Permanent restore points: Must be executed by the sys user.

    - Normal restore points: Requires FLASHBACK ANY TABLE or higher privileges.

###  Operation Steps

1. Connect and log in to the database with a user that meets the required permissions.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Query restore point information to identify target names. 

    ```sql
    SELECT NAME,SCN,IS_GUARANTEE FROM V$RESTORE_POINT;

    NAME                                                                               SCN IS_GUARANTEE
    ---------------------------------------------------------------- --------------------- ------------
    G20241201_01                                                        725539138356854784            1
    G20241201_02                                                        725542053510234112            1
    G202412011100                                                       725539138356854784            1
    N20241201_01                                                        725539138356854784            0
    N20241201_02                                                        725542222390407168            0
    N202412011100                                                       725539138356854784            0
    ```

3. Execute the [DROP RESTORE POINT](../../../开发手册/SQL参考手册/SQL语句/DROP RESTORE POINT) statement to delete the restore point.

    ```sql 
    DROP RESTORE POINT N202412011100;
    ```
