YashanDB supports manual switchover of the primary and standby database while the primary and standby database are synchronized normally. It also supports manual failover of the standby database when leader election is not enabled and the primary database is abnormal.

In the YAC Deployment, only the master node of the backup cluster supports manual switchover and manual failover when leader election is not enabled.

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the execution container for switchover or failover should be selected based on the object to be switched:
 
- Connecting to the CDB root: Switch the role of the entire CDB (including the CDB root and all PDBs)
 
- Directly connecting to PDB: Only switch the role of that PDB
 
Additionally, PDB primary-standby switching can also be performed by connecting to the CDB root for global operations. For details, please refer to [Batch PDB Primary-Standby Switching in CDB](#switchinCDB).


> **Note**:
>
> When manually executing switchover or failover, the new primary promotion process is divided into before and after changing roles. If the process fails after changing the database role, the new primary will automatically shut down and attempt to restart.
>
> For example, the startup of the XFMR (automatic transformation) module is executed after the role change. If startup fails, it will automatically shut down and restart during the promotion to primary.

## Switchover

In daily use, operations such as scheduled maintenance and upgrades of the primary database server (e.g., version updates, hardware replacements, configuration adjustments), high load on the primary database (traffic can be manually switched to the standby database as needed), and simulating the failover capability can be performed by executing the switchover operation to manually adjust the roles of the primary and standby databases.

### Precautions

- During the switchover process, all sessions connected to the primary database will be disconnected, and no new sessions can be created until the switch is completed or fails.

- If the primary/standby network is disconnected during the switchover, the switch will fail.

- If the standby database transport_lag or apply_lag is not 0 (i.e., there is redo waiting to be received and applied by the standby database), the switchover process will take longer. You can query the [V$RECOVERY_PROGRESS](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$RECOVERY_PROGRESS) view to understand the remaining apply time.

- After the switchover is complete, the primary standby database will reconnect, resulting in a brief network disconnection.

- When performing switchover on a logical standby database, if it fails, SQL Apply may be paused, and you need to execute the statement `ALTER DATABASE START LOGICAL STANDBY APPLY IMMEDIATE` to enable SQL Apply.

### Prerequisites

- The primary and standby databases are synchronized properly, and the synchronization status can be obtained via the [V$REPLICATION_STATUS](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$REPLICATION_STATUS) view.

- Switchover must be executed in the standby database.

### Steps

1. Connect and log in to the the target database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Confirm the role of the primary and standby databases.

   ```sql
   SELECT database_id,database_name,log_mode,open_mode,database_role FROM V$DATABASE;
   
   DATABASE_ID DATABASE_NAME  LOG_MODE      OPEN_MODE    DATABASE_ROLE
   ------------ -------------- ------------- ------------ --------------
      569377301 yashandb          ARCHIVELOG    READ_ONLY    STANDBY      
   ```

   If the current node is the primary database, you need to exit the session and log in to the standby database.

3. Check the synchronization status of the primary and standby databases.

   ```sql
   SELECT connection, status, peer_addr, transport_lag, apply_lag FROM V$REPLICATION_STATUS;
   CONNECTION  STATUS   PEER_ADDR              TRANSPORT_LAG    APPLY_LAG 
   ----------- -------- ---------------------- ------------- ------------ 
   CONNECTED   NORMAL   127.0.0.1:1688                     0            0
   ```

4. Execute switchover in the standby database.
   
   At this point, all transactions of the primary database will be interrupted, and the client cannot connect to the primary database during the execution of the switchover.

   ```sql
   ALTER DATABASE SWITCHOVER;
   ```

5. Repeat step 1 to check if the roles of the primary and standby databases have been swapped and if the synchronization status is normal.

## Failover

Failover is applicable when the primary database is damaged, or the server is down or unavailable, and it is necessary to perform failover immediately to restore business as quickly as possible.

### Step 1: Check and Disable Automatic Primary

Before executing the failover, you need to check whether automatic primary selection is enabled. If it is enabled, you need to disable it before executing failover.

```sql
-- Check the value of parameter HA_ELECTION_ENABLED
SHOW PARAMETER HA_ELECTION_ENABLED

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
HA_ELECTION_ENABLED                                              TRUE
```

HA_ELECTION_ENABLED = TRUE indicates that leader election is enabled. You need to [disable leader election](../../All Manuals/High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby.html#close_election) before proceeding with subsequent operations.

### Step 2: Select Standby Database for Promotion

Failover conducted in an emergency situation may result in data loss. To minimize the loss, you should select the standby database with the most log data from multiple standby databases for failover.

> **Caution**: 
>
>- Failover may lead to data loss and should only be conducted in the case of primary database failure, or it may lead to dual-primary or split-brain issues (multiple node instances providing write service).
>
>- After failover is completed, if the old primary database environment is restored, you must manually downgrade it, **do not directly OPEN it**, or it may lead to dual-primary or split-brain problems.
>
>- In maximize protection mode or maximize availability mode with normal synchronization status, choosing the standby database with the most redo to perform failover can greatly reduce the likelihood of data loss.

1. Check the `received_lfn` and `gap_seq#` fields in the `V$REPLICATION_STATUS` view:

   - If `gap_seq#` is 0 for all, choose the standby database with the largest `received_lfn` value.

   - If any standby database has a `gap_seq#` not equal to 0, it indicates that the standby database is missing some archived logs and will lose some data upon promoting. You need to check this view across all standby databases to select the one with the largest `gap_seq#` and `received_lfn`.

   ```sql
   -- Confirm the role of the primary and standby databases.
   SELECT database_id,database_name,log_mode,open_mode,database_role FROM V$DATABASE;
   DATABASE_ID DATABASE_NAME   LOG_MODE     OPEN_MODE      DATABASE_ROLE
   ------------ --------------- ------------ -------------- --------------
      569377301 yasdb           ARCHIVELOG   READ_ONLY      STANDBY      
   
   -- Confirm that the standby database connection has been disconnected and that RECEIVED_LFN is the largest among the remaining standby databases, with GAP_SEQ# equal to 0
   SELECT connection,status,received_lfn,gap_seq# FROM V$REPLICATION_STATUS;

   CONNECTION        STATUS                     RECEIVED_LFN     GAP_SEQ# 
   ----------------- ----------------- --------------------- ------------ 
   DISCONNECTED      NORMAL                             1420            0
   ```

2. Execute failover on the selected standby database, promoting the standby database to a read-write state.

   ```sql
   ALTER DATABASE FAILOVER;
   ```

3. After the failover is successfully executed, confirm the role and open mode of the standby database. 

   If `open_mode` is `READ_WRITE` and `database_role` is `PRIMARY`, it indicates that the failover operation has been successful, and that standby database has been promoted to the new primary database.

   ```sql
   SELECT database_id,database_name,log_mode,open_mode,database_role FROM V$DATABASE;
   ```

### Step 3: Downgrade Old Primary Database

After the failover is completed, if the old primary database environment is restored, you need to manually downgrade it.

> **Warn**:
>
> When starting the old primary database, strictly follow the manual downgrade process to **ensure that the old primary database is started only to the MOUNT stage and completed the downgrade before starting to the OPEN stage**.
>
> If the old primary database is directly started to the OPEN stage after new primary selection and then the downgrade operation is executed, it may lead to split-brain phenomena (i.e., the old primary database downgraded to standby becomes in NEED REPAIR status).

1. Start the old primary database instance to the MOUNT stage.

   ```shell
   # Start the old primary database (e.g., 1-1) to the MOUNT stage
   $ yasboot node start -c yashandb -n 1-1 -m mount
   ```

2. Execute the downgrade command.

   ```sql
   -- Downgrade the old primary database
   ALTER DATABASE CONVERT TO PHYSICAL STANDBY;  
   ```

3. OPEN the database.

   ```sql
   -- OPEN the database
   ALTER DATABASE OPEN;

   -- Query to check if the role has been converted
   SELECT open_mode,database_role FROM V$DATABASE;
   OPEN_MODE         DATABASE_ROLE     
   ----------------- ----------------- 
   READ_ONLY         STANDBY     
   ```

After downgrading, you can check the `connection` and `status` in the `V$REPLICATION_STATUS` view to confirm the status of the new standby database:

- If it connects to the new primary database, and the status is NORMAL, it indicates that the status of the new standby database is normal after the downgrade.

- If the status is NEED REPAIR, it indicates that a split-brain has occurred, with divergence in redo and data between the new and old primary databases. The old primary database needs to be quickly repaired or fully repaired. For detailed operations, please refer to [repairing abnormal standby databases](./​​Standby Repair).

   Generally, the possible reasons for the old primary database reporting NEED REPAIR after downgrading include:

   - In maximize performance or maximize availability mode, there may be some redo that has not been sent to the standby database before the old primary database crashed. After the standby database has been promoted and the old primary database downgraded, the old primary database will have this extra redo compared to the new primary database. If this redo contains committed transactions, the data between the old primary database and the new primary database will be inconsistent, and the old primary database cannot directly act as a standby database to the new primary database, requiring manual intervention to resolve the inconsistent state.

   - In maximize protection mode, if the selected standby database is not the one with the most redo, the old primary database may report NEED REPAIR when downgrading to connect to the new primary database, and other standby databases may also report NEED REPAIR when they connect to the new primary database. Therefore, when executing the failover operation, you should choose the standby database with the most redo whenever possible.

- If the status is REDO MISMATCH, it indicates that a split-brain has occurred, with divergence in redo between the old and new primary databases. You can quickly repair the new standby database using `ALTER SYSTEM IGNORE STANDBY MISMATCHED REDO`.

   REDO MISMATCH is similar to NEED REPAIR, but the inconsistent redo is relatively small, and the redo content has not yet been applied to the database files. Therefore, you can directly discard the inconsistent redo and start receiving and applying the new primary database's redo from the divergence point, achieving a rapid repair.

<span id="switchinCDB" name="switchinCDB" class="yaslink"></span>

## Batch PDB Primary-Standby Switching in CDB

When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), to meet different business scenario requirements and improve management efficiency while simplifying operations, the following primary-standby switching methods are supported:

- CDB-level global switching: Execute [ALTER DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE) statement or [yasboot](../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot node) command through the CDB root to perform switchover or failover, achieving unified role conversion for the entire CDB (the CDB root and all PDBs). The final switching result is that all PDBs on the new primary CDB root become primary databases

   ```shell
   # Log into the CDB root
   $ yasql sys/********@192.168.1.2:1688

   # Scenario 1: Promote the standby CDB to primary as planned
   SQL> ALTER DATABASE SWITCHOVER;
   
   # Scenario 2: After the primary CDB failure, promote the standby CDB to primary
   SQL> ALTER DATABASE FAILOVER;
   ```

- Batch PDB Precision Switching: Execute [ALTER PLUGGABLE DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/ALTER PLUGGABLE DATABASE) statement or [*yasboot*](../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot node) command through the CDB root to perform directed operations on specified single or multiple PDBs for independent switchover or failover.

   ```shell
   # Log into the CDB root
   $ yasql sys/********@192.168.1.2:1688

   # Scenario 1: Batch promote standby pdb1 and standby pdb2 to primary as planned
   SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 SWITCHOVER;

   # Scenario 2: After primary pdb3 failure, promote the standby pdb3 to primary
   SQL> ALTER PLUGGABLE DATABASE pdb3 FAILOVER;
   ```

## Execute Primary/Standby Switch with yasboot

YashanDB supports remote execution of primary/standby switch using the [yasboot](../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot node) tool with commands like `yasboot node switchover` or `yasboot node failover`.

```shell
# Scenario 1: Promote standby node 1-2 to primary as planned
$ yasboot node switchover -c yashandb -n 1-2
   
# Scenario 2: After primary database failure, promote standby node 1-2 to primary
$ yasboot node failover -c yashandb -n 1-2
```

