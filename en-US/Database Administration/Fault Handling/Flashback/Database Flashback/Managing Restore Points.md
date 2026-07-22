A Restore Point acts like a bookmark, marking a specific SCN or timestamp to facilitate locating target points for database flashback operations.

- In primary-standby HA deployments, restore points between the primary and standby databases/clusters are completely independent and not synchronized with each other. If you need to create a restore point on the standby database/standby cluster, you need to [pause redo log apply](../../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE.md#stop_apply) before executing this statement.


- When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the restore points on the CDB root and each PDB are independent of each other. You must connect to the target container to perform restore point-related management operations, and such operations will only take effect on that specific container.


Based on persistence, restore points are classified into Permanent Restore Points and Normal Restore Points: 

| Features  | Permanent Restore Points | Normal Restore Points |
|--------------------|-------------------|-------------------|
| Flashback Log Dependency | Force retention of flashback logs after the permanent restore point | Flashback log files may be automatically cleaned; normal restore points become invalid after associated logs are removed |
| Storage Overhead | High<br/>Flashback logs must be retained permanently  | Low<br/>Only timestamps or SCNs are recorded |
| Deletion Mechanism | Manual deletion only | * Manual deletion allowed<br/>Some restore points are deleted when flashback log files are removed, including: when storage space for flashback log files becomes insufficient, flashback log files are automatically cleaned up; after flashback database operations, flashback log files older than the target time point are automatically cleaned up.<br />* All normal restore points are automatically deleted when Flashback is disabled |
| Impact of recovery operations on restore points | * For [backup and recovery database](../../../Backup and Recovery/00Backup and Recovery): After recovery, all restore points will be automatically deleted and the database flashback functionality  will be turned off (i.e., restored to the default state).<br/>* For [flashback database](./Performing Flashback Database): After the database flashback, any restore point created after the target recovery time point will be automatically deleted | Same as permanent restore point |
| Additional Notes | * Mandatory retention of [archive log files](../../../Instance Management/Archive Management.md#auto_cleanup) <br/>* Normally cannot [disable database flashback](./Database Flashback Basic Configuration.md#flashback_off) when permanent restore points exist, but flashback on old primary is forcibly and automatically disabled during SWITCHOVER (even if permanent restore points exist) | - |

##  Creating Restore Points

Before performing high-risk operations, you can create a restore point to mark a known good state and select either a permanent or normal restore point based on recovery requirements.

###  Prerequisites



- The database (or container) currently has the [database flashback](./Database Flashback Basic Configuration) functionality  enabled.

- The database (or container) instance on which the operation is performed must be in the MOUNT or OPEN state.

- User privileges:

    - Permanent Restore Points: Requires SYSDBA privileges.

    - Normal Restore Points: Requires FLASHBACK ANY TABLE privilege or higher.

 

- When performing this operation on a standby cluster in Primary-Standby Cluster Deployment, you must log in to its master instance (INSTANCE_ROLE=MASTER in the GV$INSTANCE view).

- The restore point's SCN must be later than the OLDEST_FLASHBACK_SCN value in [V$FLASHBACK_DATABASE_LOG](../../../../Reference Manual/System Views/Dynamic Performance Views/V$FLASHBACK_DATABASE_LOG).

###  Operation Steps

::: tabs
== Permanent Restore Points

1. Connect and log in to the target database (or container) with a user that meets the required permissions.

    ```shell
    # Log in to a non-CDB or the CDB root of a CDB
    $ yasql / as sysdba

    # Log in to a PDB in a CDB
    $ yasql sys/********@192.168.1.2:1688/pdb1
    ```
    

2. If the current logged-in entity is a standby database/standby cluster, the redo log apply should be paused before proceeding with subsequent operations.

    ```sql
    -- Check the database status
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    OPEN
    
    -- If it is in the OPEN state, manually stop redo log apply
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;

    -- If it is in the MOUNTED state, its redo log application has not started yet (no additional operation is required, directly proceed to the next step).
    ```
    

3. Create permanent restore points using [CREATE RESTORE POINT](../../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE RESTORE POINT):

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
4. If the redo log application was stopped in the previous steps, it needs to be resumed.

    ```sql
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
    ```
    

== Normal Restore Points

1. Connect and log in to the target database (or container) with a user that meets the required permissions.

    ```shell
    # Log in to a non-CDB or the CDB root of a CDB
    $ yasql / as sysdba

    # Log in to a PDB in a CDB
    $ yasql sys/********@192.168.1.2:1688/pdb1
    ```
    

2. If the current logged-in entity is a standby database/standby cluster, the redo log apply should be paused before proceeding with subsequent operations.

    ```sql
    -- Check the database status
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    OPEN
    
    -- If it is in the OPEN state, manually stop redo log apply
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;

    -- If it is in the MOUNTED state, its redo log application has not started yet (no additional operation is required, directly proceed to the next step).
    ```
    

3. Create normal restore points using [CREATE RESTORE POINT](../../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE RESTORE POINT):

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
4. If the redo log application was stopped in the previous steps, it needs to be resumed.

    ```sql
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
    ```
    
:::

##  Viewing Existing Restore Points

The [GV$RESTORE_POINT](../../../../Reference Manual/System Views/Dynamic Performance Views/GV$RESTORE_POINT) and [V$RESTORE_POINT](../../../../Reference Manual/System Views/Dynamic Performance Views/V$RESTORE_POINT) views contain information about restore points. Before performing a database flashback, you can first query these views to identify the target time point for recovery.

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

To [disable database flashback](./Database Flashback Basic Configuration), all permanent restore points must be manually deleted first.

###  Prerequisites



- The database (or container) currently has the [database flashback](./Database Flashback Basic Configuration) functionality  enabled.

- The database (or container) instance on which the operation is performed must be in the MOUNT or OPEN state.

- User privileges:

    - Permanent Restore Points: Requires SYSDBA privileges.

    - Normal Restore Points: Requires FLASHBACK ANY TABLE privilege or higher.

 

###  Operation Steps

1. Connect and log in to the target database (or container) with a user that meets the required permissions.

    ```shell
    # Log in to a non-CDB or the CDB root of a CDB
    $ yasql / as sysdba

    # Log in to a PDB in a CDB
    $ yasql sys/********@192.168.1.2:1688/pdb1
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

3. Execute the [DROP RESTORE POINT](../../../../Development Guide/SQL Reference Manual/SQL Statements/DROP RESTORE POINT) statement to delete the restore point.

    ```sql 
    DROP RESTORE POINT N202412011100;
    ```
