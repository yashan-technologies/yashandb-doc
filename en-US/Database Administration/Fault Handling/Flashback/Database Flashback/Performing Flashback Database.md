Flashback can roll back almost all [database operations](./00Database Flashback.md#DataScope) during the specified [time range](./00Database Flashback.md#DataScope), excluding data files.

According to the object to be flashed back, the operation steps in this article are divided into the following scenarios:

- Flashback the entire database (in primary-standby HA deployment, both the primary and standby databases will be rolled back)

   When YashanDB is deployed as a CDB (with the configuration parameter ENABLE_PLUGGABLE_DATABASE = TRUE), "flash back the entire database" extends to either "flash back the CDB root" (i.e., log in to the CDB root for operations)  or "flash back a specific PDB" (i.e., log in to a specific PDB for operations).

- Flashback a specified standby database in primary-standby HA deployment

   In Primary-Standby Cluster Deployment, "standby database" extends to "standby cluster," which may be directly referred to as "standby database" in subsequent text.

The database flashback functionality is unavailable in ISC Distributed Cluster Deployment.

##  Flashback the Entire Database

During the process of flashing back the entire database, it may affect the continuous operation of the database:

- For Standalone Deployment (single-node) database or YAC/Distributed Cluster Deployment (single-cluster), it is required to start/restart the database to the MOUNT stage.

- For primary-standby HA deployment, it is required to start/restart the primary database to the MOUNT stage first. After the flashback is completed, it is required to restart all standby databases to maintain synchronization. The target time point T2 of the standby database's flashback must not be later than the target time point T1 reached by the primary database's flashback. Otherwise, a split-brain problem between the primary and standby databases will occur:

   - T2 = T1: Normal

   - T2 is earlier than T1: Normal, and the standby database will automatically catch up with the primary database

   - T2 is later than T1: There will be a split-brain situation between the primary and standby databases, and the standby database will be placed in the "need repair" state.

   > **Note**:
   >
   > In Primary-Standby Cluster Deployment, "primary database" extends to "primary cluster," which may be directly referred to as "primary database" in subsequent text.

### Prerequisites

- Ensure that the database (or primary database) has enabled the [database flashback](./Database Flashback Basic Configuration) functionality is enabled . You can confirm this by querying the FLASHBACK_ON field in the V$DATABASE view.

- The flashback target time point (i.e., the time point to which to restore) has been confirmed, and the corresponding SCN, timestamp, or restore point information for this time point has been obtained.
  - For SCN/timestamp-based flashback, refer to [Recoverable Time Range](./00Database Flashback) to determine the earliest recoverable point.

  - For restore point-based flashback, first [create a restore point](./Managing Restore Points) and note its name.

- In primary-standby ha deployment, it is also necessary to confirm whether the database flashback function has been enabled for the standby database and whether it can be flashed back to the above-mentioned target time point. The confirmation results will determine the subsequent operation method for making the standby database catch up with the primary database:

   - If the answers to both questions are yes, the standby database can be made to catch up directly through in-place flashback.

   - Otherwise, the standby database needs to be re-built (BUILD DATABASE).
   
### Operation Steps

<span id="Step1" name="Step1"></span>

#### Step 1: Temporarily Disable Leader Election

This operation needs to be carried out only in an environment where primary-standby HA deployment is adopted and the database is not in a shutdown state.

- Log in to the server where the primary database or any instance of the primary cluster is located as the installation user, check and disable the yasom election

   ```shell
   # Check whether yasom election is enabled (the echo information is for reference only)
   $ yasboot election config show -c yashandb
   group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     0 seconds
                      Apply Rate:    3.79 MByte/s

      Properties:
        FailoverThreshold      = 9
        FailoverAutoReinstate  = false
        ZeroDataLossMode       = true

   Automatic Failover: Enabled in Zero Data Loss Mode # Enabled indicates that yasom election is enabled

   # If it is enabled, disable yasom election
   $ yasboot election enable off -c yashandb
   ```

- Log in to the server where the primary database or any instance of the primary cluster is located as the installation user, check and disable the leader election

   ```shell
   # Check whether the leader election is enabled (the echo information is for reference only)
   $ yasboot cluster config show -c yashandb -q HA_ELECTION_ENABLED
   +------------------------------+
   | node   | HA_ELECTION_ENABLED |
   +------------------------------+
   | db-1-1 | TRUE                |
   +--------+---------------------+
   | db-1-2 | TRUE                |
   +--------+---------------------+
   # TRUE indicates that is leader election enabled

   # If enabled, you need to disable leader election
   $ yasboot cluster config set -c yashandb -k HA_ELECTION_ENABLED -v FALSE
   ```

#### Step 2: Start/Restart the database to MOUNT Stage

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Start/Restart the database (or the primary database) to the MOUNT stage.

   >**Note**:
   >
   > Operations vary to some extent depending on different deployment forms. For the same deployment form, operations in a non-CDB environment are exactly the same as those in the CDB root. However, in a CDB, the operation steps of PDB are different from those of the CDB root.

   ::: tabs
   == Standalone (Single Node) Deployment

```shell
$ yasboot cluster restart -c yashandb -m mount
```
   == Standalone (Primary-Standby) Deployment

```shell
# View database information and obtain the nodeid of the target instance
$ yasboot cluster status -c yashandb -d

# Restart the primary database to MOUNT
$ yasboot node restart -c yashandb -n 1-1 -m mount
```

    == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, only one instance needs to be in the MOUNT stage while all other instances should be in the OFF state.

```shell
# Check the database instance startup method (AUTO_START parameter)
$ ycsctl get AUTO_START
YCS AUTO_START = ALWAYS
        
# Stop the database cluster
$ yasboot cluster stop -c yashandb

# Start the database instance on the current server to MOUNT stage
# If AUTO_START = ALWAYS (default value), execute the following command to directly start YCS instance and database instance
$ ycsctl start ycs -m mount

# If AUTO_START = NEVER, execute the following two commands to start YCS instance first, then the database instance
$ ycsctl start ycs 
$ ycsctl start instance -m mount
```

   == PDB

```shell
# View PDB information and obtain the nodeid of the target PDB instance
$ yasboot pdb status -c yashandb --pdb pdb1

# Restart the primary PDB
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1 -m mount
```
    :::

3. Connect and log in to the target PDB as the sys user.

4. Check the database status.

    ```sql
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    MOUNTED
    ```


<span id="Step3" name="Step3"></span>

#### Step 3: Perform Database Flashback

1. Execute flashback based on target recovery point:

   ```sql
   -- Scenario 1: Flashback to restore point
   FLASHBACK DATABASE TO RESTORE POINT G20241201_01;

   -- Scenario 2: Flashback to SCN
   FLASHBACK DATABASE TO SCN 725539138356854784;

   -- Scenario 3: Flashback to timestamp 
   FLASHBACK DATABASE TO TIME '2025-08-12 11:58:05.847545';
   ```

2. Open the database (or the primary database) and reset the redo timeline.

   ::: tabs
   == Standalone (Primary-Standby) Deployment or PDB

```sql
ALTER DATABASE OPEN RESETLOGS;
```
   == Non-CDB or the CDB root in YAC/Distributed Cluster Deployment 

```shell
SQL> ALTER DATABASE OPEN RESETLOGS;
SQL> exit

# View database information and obtain the nodeid of the target instances
$ yasboot cluster status -c yashandb -d

# Start other instances of the current cluster, for example:
$ yasboot node start -c yashandb -n 2-2
```

   :::

#### Step 4: Standby Database(s) Catch Up with the Primary Database

This operation needs to be carried out only in primary-standby HA deployment.

1. Select the method for the standby database to catch up with the primary database according to the actual situation:

   - If the standby database has the flashback functionality enabled and can be flashed back to a time point no later than that of the primary database in [Step 3](#Step3), it can catch up by flashing back the standby database to the target time point. For specific operations, please refer to [Standby Flashback in Place](#inplace).

   - If the flashback functionality is not enabled for the standby database or it cannot be flashed back to the target time point, the only way to make it catch up is to [re - build the standby database](../../../../Development Guide/SQL Reference Manual/SQL Statements/BUILD DATABASE) (BUILD DATABASE).

2. If the leader election was temporarily disabled in [Step 1](#Step1), the configuration needs to be restored.

   ```shell
   SQL> exit

   # If you need to restore the configurations related to yasom election
   $ yasboot election enable on -c yashandb

   # If you need to restore the configurations related to leader election
   $ yasboot cluster config set -c yashandb -k HA_ELECTION_ENABLED -v TRUE
   ```

##   Flashback the Standby Database

In primary-standby HA deployment, flashback the standby database requires choosing the corresponding scenario according to actual operation and maintenance needs. The operation steps vary for different scenarios. The specific classification and explanations are as follows:

- [Standby Flashback in Place](#inplace): Perform flashback operations on the standby database without altering the existing primary-standby architecture. During the flashback process, the primary-standby synchronization relationship remains unchanged, and the primary database runs normally with business writes unaffected. The applicable scenarios mainly include:

   - Post-operations for primary database flashback or recovery from standby database split-brain: When a split-brain phenomenon occurs in the standby database (all standby databases experience split-brain after the primary database is flashed back), resulting in abnormal data synchronization between the primary and standby databases, flash back the standby database in-place to the normal state before the split-brain occurred to quickly restore the primary-standby synchronization relationship.

   - Data Recovery Verification: After the primary database experiences misoperations or data corruption, perform flashback operations on the standby database to verify the feasibility and completeness of data recovery, providing a basis for subsequent primary database recovery without affecting primary database business.

   - Historical Data Retrieval: After the primary database suffers accidental data deletion or modification, without interrupting the primary database or business writes, flashback the standby database to the time point before data loss, extract the historical version data before the misoperation, and then import it into the primary database to complete data recovery.

- [Standby Flashback for Drill Calibration](#drill): In some scenarios, the target standby database may need to be temporarily detached from the primary-standby architecture (releasing its synchronization relationship with the primary database) to perform data operations separately (such as business simulation, testing, etc.). After the operation is complete, if it is necessary to rejoin the standby to the primary-standby architecture, flashback can be used to repair its primary-standby synchronization status.
   
   Standby temporary detachment is recommended only in one primary and multi-standby deployment, and the main applicable scenarios include:

   - Primary-Standby Business Simulation Drill: During business off-peak hours, detach a certain standby database from the primary-standby architecture and use the detached standby to simulate subsequent important business operations (such as system upgrades, large-scale data imports, business process changes, etc.), proactively identifying operation risks and verifying business compatibility. After the drill completes, rejoin the standby to the primary-standby architecture. The entire process does not affect the normal business operations of the primary database.

<span id="inplace" name="inplace"></span>

### Standby Flashback in Place

#### Prerequisites

- Ensure the target standby database has enabled the [database flashback](./Database Flashback Basic Configuration) functionality is enabled. You can confirm this by querying the FLASHBACK_ON field in the V$DATABASE view.

- Confirm the target recovery point (SCN, timestamp, or restore point):

  - For SCN/timestamp-based flashback, refer to [Recoverable Time Range](./00Database Flashback) to determine the earliest recoverable point.

  - For restore point-based flashback, first [create a restore point](./Managing Restore Points) and note its name.

#### Operation Steps

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Start/Restart the target standby database (or instance) to the MOUNT stage.

   >**Note**:
   >
   > Operations vary to some extent depending on different deployment forms. For the same deployment form, operations in a non-CDB environment are exactly the same as those in the CDB root. However, in a CDB, the operation steps of PDB are different from those of the CDB root.


   ::: tabs
   == Standalone (Primary-Standby) Deployment

```shell
# View database information and obtain the nodeid of the target instances
$ yasboot cluster status -c yashandb -d

# Restart the target standby database
$ yasboot node restart -c yashandb -n 1-2 -m mount
```

   == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, only one instance needs to be in the MOUNT stage while all other instances should be in the OFF state.

```shell
# Check the database instance startup method (AUTO_START parameter)
$ ycsctl get AUTO_START
YCS AUTO_START = ALWAYS
        
# Stop the database cluster
$ yasboot cluster stop -c yashandb

# Start the database instance on the current server to MOUNT stage
# If AUTO_START = ALWAYS (default value), execute the following command to directly start YCS instance and database instance
$ ycsctl start ycs -m mount

# If AUTO_START = NEVER, execute the following two commands to start YCS instance first, then the database instance
$ ycsctl start ycs 
$ ycsctl start instance -m mount
```

   == PDB

```shell
# View PDB information and obtain the nodeid of the target PDB instance
$ yasboot pdb status -c yashandb --pdb pdb1

# Restart the primary PDB
$ yasboot pdb restart -c yashandb -n 1-2 --pdb pdb1 -m mount
```

    :::
    

3. Connect and log in to the target PDB as the sys user.

4. Check the database status.

    ```sql
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    MOUNTED
    ```


5. Execute flashback based on target recovery point:

   ```sql
   -- Scenario 1: Flashback to restore point
   FLASHBACK DATABASE TO RESTORE POINT G20241201_01;

   -- Scenario 2: Flashback to SCN
   FLASHBACK DATABASE TO SCN 725539138356854784;

   -- Scenario 3: Flashback to timestamp 
   FLASHBACK DATABASE TO TIME '2025-08-12 11:58:05.847545';
   ```

6. Open the target standby database (or instance).

   ```sql
   ALTER DATABASE OPEN;
   ```

7. Enable the redo log replay of the target standby database.

   ```sql
   ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
   ```

8. Check whether the primary-standby status is normal.

   ```sql
   SELECT THREAD#,CONNECTION,STATUS,PEER_ROLE,PEER_ADDR FROM V$REPLICATION_STATUS;
   ```

9. In Primary-Standby Cluster Deployment, it is necessary to start other instances of the target cluster.
   
   ```shell
   SQL> exit

   # View database information and obtain the nodeid of the target instances
   $ yasboot cluster status -c yashandb -d

   # Start other instances of the current cluster, for example:
   $ yasboot node start -c yashandb -n 2-2
   ```
   

#### Follow-up Operations

After the standby database is flashed back, its archived log files and redo files will not be cleaned up. You can manage them as needed, for example, [clean up unnecessary archived log files](../../../Storage Management/Database File Management/Archive Log File Management.md#cleanup_manual).

<span id="drill" name="drill"></span>

### Standby Flashback for Drill Calibration

####  Step 1: Select and Prepare the Target Standby Database

1. Connect and log in to the database as a DBA user.

2. View information such as the database protection mode and primary-standby link configuration, and select the standby database on which to perform the drill operation (hereinafter referred to as the "drill standby database").

   ::: tabs
   == Standalone (Primary-Standby) Deployment

```sql
-- View ARCHIVE_DEST_n configuration  
SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'ARCHIVE_DEST_%' AND VALUE IS NOT NULL;
   
NAME                                                             VALUE                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
ARCHIVE_DEST_2                                                   SERVICE=192.168.1.3:1688 NODE_ID=1-2:2
ARCHIVE_DEST_3                                                   SERVICE=192.168.1.4:1688 NODE_ID=1-3:3
   
-- View protection mode
SELECT PROTECTION_MODE,PROTECTION_LEVEL FROM V$DATABASE;
PROTECTION_MODE                                                  PROTECTION_LEVEL                                       
---------------------------------------------------------------- ----------------------------------------------------------------
MAXIMUM PROTECTION                                               MAXIMUM PROTECTION
   
-- If the protection mode is MAXIMUM PROTECTION, also view the synchronized standby
SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME = 'REQUIRED_SYNC_STANDBYS' OR NAME = 'QUORUM_SYNC_STANDBYS';
   
NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
QUORUM_SYNC_STANDBYS                                             MAJORITY
REQUIRED_SYNC_STANDBYS
```

Select the operation node for business simulation based on the configuration information (this chapter uses node 1-3 as an example). It is recommended to choose a standby database node that is not explicitly defined as a synchronized standby to simplify operations. Additionally, please record the above configuration information, as the corresponding configuration needs to be restored after completing the simulation operations.

   == Primary-Standby Cluster Deployment

```sql
-- View ARCHIVE_DEST_n configuration  
SELECT INST_ID,NAME,VALUE FROM GV$PARAMETER WHERE NAME LIKE 'ARCHIVE_DEST_%' AND VALUE IS NOT NULL;

    INST_ID NAME                                                             VALUE
----------- ---------------------------------------------------------------- ----------------------------------------------------------------
          1 ARCHIVE_DEST_2                                                   SERVICE=192.168.10.4:1688,192.168.10.5:1688 NODE_ID=2-1:1
          2 ARCHIVE_DEST_2                                                   SERVICE=192.168.10.4:1688,192.168.10.5:1688 NODE_ID=2-1:1

-- View protection mode
SELECT INST_ID,PROTECTION_MODE,PROTECTION_LEVEL FROM GV$DATABASE;

    INST_ID PROTECTION_MODE                                                  PROTECTION_LEVEL
----------- ---------------------------------------------------------------- ----------------------------------------------------------------
          1 MAXIMUM PERFORMANCE                                              MAXIMUM PERFORMANCE
          2 MAXIMUM PERFORMANCE                                              MAXIMUM PERFORMANCE

-- If the protection mode is MAXIMUM PROTECTION, also view the synchronized standby
SHOW PARAMETER REQUIRED_SYNC_STANDBYS;
```

Select the target standby cluster for business simulation based on the configuration information. It is recommended to choose a standby cluster that is not explicitly defined as a synchronized standby to simplify operations. Additionally, please record the above configuration information, as the corresponding configuration needs to be restored after completing the simulation operations.

   :::

3. Execute the following operations on the primary database to temporarily disassociate the primary database from the drill standby databases.

   ::: tabs
   == Standalone (Primary-Standby) Deployment

1. ) Connect and log in to the target database node as a DBA user.

2. ) Adjust the protection mode and [synchronized standby](../../../../High Availability/Defining Synchronous Standbys) configuration based on the primary-standby scale after removing drill standby database 1-3.

   ```sql
   -- Scenario 1: If only the primary database remains, change to maximum performance
   ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PERFORMANCE;
   
   -- Scenario 2: If 1 primary and 1 standby remain, change to maximum availability
   ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE AVAILABILITY;

   -- Scenario 3: If there is still one primary database and multiple standby databases, there is no need to adjust the protection mode
   -- But it is necessary to ensure that standby database 1-3 are not explicitly designated as synchronous standbys
   SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME = 'REQUIRED_SYNC_STANDBYS' OR NAME = 'QUORUM_SYNC_STANDBYS';

   -- If standby database 1-3 is explicitly designated as synchronous standby databases, then the corresponding parameters also need to be adjusted
   ```

   == Primary-Standby Cluster Deployment

1. ) Connect and log in to an instance of the target cluster as a DBA user.

2. ) Check the [synchronous standby](../../../../High Availability/Defining Synchronous Standbys) configuration to ensure that the target cluster is not explicitly designated as a synchronous standby.

   ```sql
   SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME = 'REQUIRED_SYNC_STANDBYS';

   -- If the target cluster is explicitly designated as synchronous standby databases, then the corresponding parameters also need to be adjusted
   ```
   :::

4. On **the drill standby database**, perform the following operations to temporarily disconnect it from the primary database.

   ::: tabs
   == Standalone (Primary-Standby) Deployment

1. ) Connect and log in to the target database node as a DBA user.

2. ) View the REPLICATION_ADDR, ARCHIVE_DEST_n, and REQUIRED_SYNC_STANDBYS parameter configurations, record the parameter values, and then clear all these parameters.

   ```sql
   -- REPLICATION_ADDR
   SHOW PARAMETER REPLICATION_ADDR;
   ALTER SYSTEM SET REPLICATION_ADDR ='' SCOPE=SPFILE;
   
   -- ARCHIVE_DEST_n
   SELECT name,value FROM V$PARAMETER WHERE ( NAME LIKE 'ARCHIVE_DEST_%' ) AND ( value IS NOT NULL );
   -- Clear all ARCHIVE_DEST_n values sequentially based on the query results  
   ALTER SYSTEM SET ARCHIVE_DEST_1 ='' SCOPE=BOTH;
   ALTER SYSTEM SET ARCHIVE_DEST_2 ='' SCOPE=BOTH;
   ……
   
   -- REQUIRED_SYNC_STANDBYS
   SHOW PARAMETER REQUIRED_SYNC_STANDBYS;
   -- If the parameter value is not empty, clear it
   ALTER SYSTEM SET REQUIRED_SYNC_STANDBYS ='' SCOPE=BOTH;
   ```

3. ) Log in to the server where the target node is located as the installation user, and restart the drill standby database to make the configuration take effect.

   ```shell
   $ yasboot node restart -c yashandb -n 1-3
   ```

   == Primary-Standby Cluster Deployment

1. ) Connect and log in to an instance of the target cluster as a DBA user.

2. ) View the REPLICATION_ADDR, ARCHIVE_DEST_n, and REQUIRED_SYNC_STANDBYS parameter configurations, record the parameter values, and then clear all these parameters.

   ```sql
   -- REPLICATION_ADDR
   SHOW PARAMETER REPLICATION_ADDR;
   ALTER SYSTEM SET REPLICATION_ADDR ='' SCOPE=SPFILE;
   
   -- ARCHIVE_DEST_n
   SELECT name,value FROM V$PARAMETER WHERE ( NAME LIKE 'ARCHIVE_DEST_%' ) AND ( value IS NOT NULL );
   -- Clear all ARCHIVE_DEST_n values sequentially based on the query results  
   ALTER SYSTEM SET ARCHIVE_DEST_2 ='' SCOPE=BOTH;
   ……
   
   -- REQUIRED_SYNC_STANDBYS
   SHOW PARAMETER REQUIRED_SYNC_STANDBYS;
   -- If the parameter value is not empty, clear it
   ALTER SYSTEM SET REQUIRED_SYNC_STANDBYS ='' SCOPE=BOTH;
   ```

3. ) Log in to the server where the target node is located as the installation user, and restart the drill standby database to make the configuration take effect.

   ```shell
   $ yasboot group restart -c yashandb -g 2
   ```

4. ) Connect and log in to an instance of the target cluster as a DBA user.

5. ) Obtain the master instance information of this cluster. 

   ```sql
   SELECT INST_ID,HOST_NAME FROM GV$INSTANCE WHERE INSTANCE_ROLE = 'MASTER_ROLE';
   ```
   
   :::

<span id="Step_2" name="Step_2"></span>

####  Step 2: Complete Flashback Basic Configuration on the Drill Standby Database

1. Log in to the server where the target node is located as the installation user:

   - Standalone (Primary-Standby) Deployment: The drill standby database.

   - Primary-Standby Cluster Deployment: The master instance of the drill standby cluster.

   

2. Connect and log in to the target database node as the sys user.

3. Check and ensure that the database flashback has been enabled.

   ```sql
   SELECT FLASHBACK_ON FROM V$DATABASE;

   FLASHBACK_ON
   -----------------
   NO
   -- FLASHBACK_ON = NO indicates flashback is disabled. Proceed to enable it as needed.
   -- FLASHBACK_ON = YES indicates flashback is enabled.

   -- Enable database flashback
   ALTER DATABASE FLASHBACK ON;
   ```

4. Switch the role of the drill standby database to primary. After the switch, the database will automatically create a restore point.

   ```sql
   -- View existing restore points
   SELECT NAME,SCN,IS_GUARANTEE FROM V$RESTORE_POINT;

   -- Switch its role
   ALTER DATABASE FAILOVER;

   -- View the restore points again and record the name of the new restore point for subsequent flashback operations
   SELECT NAME,SCN,IS_GUARANTEE FROM V$RESTORE_POINT;

   -- If the restore point is not successfully created, it can be created manually
   CREATE RESTORE POINT G202412011100 GUARANTEE FLASHBACK DATABASE;
   ```

####  Step 3: Execute Simulation Business

On the drill standby database, execute the corresponding simulation business.

####  Step 4: Revert HA Configuration

1. Perform the following operations on the **drill standby database** to restore it to the primary-standby architecture.

   1. ) Log in to the server where the target node is located as the installation user:

   - Standalone (Primary-Standby) Deployment: The drill standby database.

   - Primary-Standby Cluster Deployment: The master instance of the drill standby cluster.

   

   2. )Start/Restart the target standby database (or instance) to the MOUNT stage.

   >**Note**:
   >
   > Operations vary to some extent depending on different deployment forms. For the same deployment form, operations in a non-CDB environment are exactly the same as those in the CDB root. However, in a CDB, the operation steps of PDB are different from those of the CDB root.


   ::: tabs
   == Standalone (Primary-Standby) Deployment

```shell
# View database information and obtain the nodeid of the target instances
$ yasboot cluster status -c yashandb -d

# Restart the target standby database
$ yasboot node restart -c yashandb -n 1-3 -m mount
```

   == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, only one instance needs to be in the MOUNT stage while all other instances should be in the OFF state.

```shell
# Check the database instance startup method (AUTO_START parameter)
$ ycsctl get AUTO_START
YCS AUTO_START = ALWAYS
        
# Stop the database cluster
$ yasboot cluster stop -c yashandb

# Start the database instance on the current server to MOUNT stage
# If AUTO_START = ALWAYS (default value), execute the following command to directly start YCS instance and database instance
$ ycsctl start ycs -m mount

# If AUTO_START = NEVER, execute the following two commands to start YCS instance first, then the database instance
$ ycsctl start ycs 
$ ycsctl start instance -m mount
```

   == PDB

```shell
# View PDB information and obtain the nodeid of the target PDB instance
$ yasboot pdb status -c yashandb --pdb pdb1

# Restart the primary PDB
$ yasboot pdb restart -c yashandb -n 1-3 --pdb pdb1 -m mount
```

    :::
    

   3. ) Connect and log in to the drill standby database as the sys user.

   4. ) Restore the configurations of parameters such as REPLICATION_ADDR, ARCHIVE_DEST_n, or REQUIRED_SYNC_STANDBYS on the drill standby database to the values recorded in the previous operations.

   5. ) Restart the drill standby database to the MOUNT stage again to make the configuration take effect.
   
   6. ) Force the drill standby database to be demoted to a standby.

      ```sql
      ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
      ```

2. Execute the following operations on the **primary** database to restore the drill standby database to the primary-standby architecture.

   1. ) Connect and log in to the database as a DBA user.

   2. ) Restore the protection mode, or synchronized standby related configurations on the primary database to the values recorded previously.

####  Step 5: Flashback Standby Database

1. Flash back the drill standby database to the restore point recorded in [Step 2](#Step_2).

   ```sql
   FLASHBACK DATABASE TO RESTORE POINT G202501011000;
   ```

2. Check whether the primary-standby status is normal.

   ```sql
   SELECT THREAD#,CONNECTION,STATUS,PEER_ROLE,PEER_ADDR FROM V$REPLICATION_STATUS;
   ```

3. Start the drill standby database (or the current instance of the drill standby cluster) to the OPEN state.

   ```sql
   ALTER DATABASE OPEN;
   ```

4. In Primary-Standby Cluster Deployment, it is necessary to start other instances of the drill standby cluster.
   
   ```shell
   SQL> exit

   # View database information and obtain the nodeid of the target instances
   $ yasboot cluster status -c yashandb -d

   # Start other instances of the current cluster, for example:
   $ yasboot node start -c yashandb -n 2-2
   ```

#### Follow-up Operations

After the standby database is flashed back, its archived log files and redo files will not be cleaned up. You can manage them as needed, for example, [clean up unnecessary archived log files](../../../Storage Management/Database File Management/Archive Log File Management.md#cleanup_manual).
