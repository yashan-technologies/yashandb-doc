Flashback can roll back most [database operations](./00Database Flashback.md#TimeRange) during the specified [time range](./00Database Flashback.md#DataScope), excluding data files.

##  Primary Database Flashback

Performing primary database flashback requires starting/restarting the database to the MOUNT stage. After the flashback is complete, you must execute [ALTER DATABASE OPEN RESETLOGS](../../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE.md#startupclauses) to reset the redo timeline.

### Prerequisites

- Ensure that the [full database flashback](./Database Flashback Basic Configuration) functionality is enabled. You can confirm this by querying the FLASHBACK_ON field in the V$DATABASE view.



- When the database is in the MOUNT stage, only the sys user is allowed to log in.


- Confirm the target recovery point (SCN, timestamp, or restore point):

    - For SCN/timestamp-based flashback, refer to [Recoverable Time Range](./00Database Flashback) to determine the earliest recoverable point.

    - For restore point-based flashback, first [create a restore point](./Managing Restore Points) and note its name.

### Operation Steps

#### Step 1: Restart to MOUNT State

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Restart the database (or instance) to the MOUNT state.

    ::: tabs
    == Standalone Deployment

```shell
$ yasboot cluster restart -c yashandb -m mount
```

    == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, only one instance needs to be in the MOUNT state while all other instances should be in the OFF state.

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
    :::

3. Log in to the database as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

4. Check the database status.

    ```sql
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    MOUNTED
    ```


#### Step 2: Perform Database Flashback

Execute flashback based on target recovery point:

```sql
-- Method 1: Flashback to restore point
FLASHBACK DATABASE TO RESTORE POINT G20241201_01;

-- Method 2: Flashback to SCN
FLASHBACK DATABASE TO SCN 725539138356854784;

-- Method 3: Flashback to timestamp 
FLASHBACK DATABASE TO TIME '2025-08-12 11:58:05.847545';
```

#### Step 3: Open the Database

Open the database and reset the redo timeline.

```sql
ALTER DATABASE OPEN RESETLOGS;
```

The flashback operation is now complete. Verify data consistency to confirm recovery success.

##  Standby Database Flashback

Standby database flashback can only be used for primary-standby business simulation drills. For example, during business downtime, use the standby database to simulate important subsequent business operations in advance (without affecting the normal use of the primary database), identify risks early, and then flashback the standby database to its pre-drill state after the drill is completed.

>**Note**:
>
> In Primary-Standby YAC/Distributed Cluster Deployment, the term "standby database" extends to "standby cluster".

### Standby Database Flashback in Standalone Deployment

####  Step 1: Select and Prepare the Target Standby Database

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the database's protection mode and primary-standby link configuration information.

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

3. Execute the following operations on the primary database to temporarily disassociate the primary database from the target standby databases.

    1. ) Adjust the protection mode and synchronized standby configuration based on the primary-standby scale after removing target standby databases 1-3.

        - If only the primary database remains, change to maximum performance.

        ```sql
        ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PERFORMANCE;
        ```

        - If 1 primary and 1 standby remain, change to maximum availability.

        ```sql
        ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE AVAILABILITY;
        ```

        - If 1 primary and multiple standbys remain, no adjustment of protection mode is needed, but ensure that standby databases 1-3 are not explicitly designated as [synchronized standbys](../../../../High Availability/Defining Synchronous Standbys). Otherwise, the QUORUM_SYNC_STANDBYS or REQUIRED_SYNC_STANDBYS parameters also need to be modified accordingly.

    2. ) Clear the standby ARCHIVE_DEST_n parameters corresponding to node 1-3.

        ```sql
        ALTER SYSTEM SET ARCHIVE_DEST_3 ='' SCOPE=BOTH;
        ```

4. Connect and log in to the target database node as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


5. View the REPLICATION_ADDR, ARCHIVE_DEST_n, and REQUIRED_SYNC_STANDBYS parameter configurations, record the parameter values, and then clear all these parameters.

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

    At this point it becomes equivalent to a standalone single database.  

####  Step 2: Complete Flashback Basic Configuration on the Target Standby Database

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Start the target database node to MOUNT stage.

    ```shell
    $ yasboot node restart -c yashandb -n 1-3 -m mount
    ```


3. Connect and log in to the target database node as the sys user.
    ```shell
    $ yasql sys/********@192.168.1.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    



4. Enable database flashback: 

    ```sql
    ALTER DATABASE FLASHBACK ON;
    ```

5. Create a permanent restore point.

    ```sql
    CREATE RESTORE POINT G202501011000 AS OF TIMESTAMP SYSTIMESTAMP() GUARANTEE FLASHBACK DATABASE;
    ```



6. Startup the target node to the OPEN stage.  

    ```sql
    ALTER DATABASE OPEN;
    ```

6. The target node executes failover.

    ```sql
    ALTER DATABASE FAILOVER;
    ```

####  Step 3: Execute Simulation Business

On the target standby database, execute the corresponding simulation business.

####  Step 4: Flashback Standby Database

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Start the target database node to MOUNT stage.

    ```shell
    $ yasboot node restart -c yashandb -n 1-3 -m mount
    ```


3. Connect and log in to the target database node as the sys user.
    ```shell
    $ yasql sys/********@192.168.1.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

4. Perform flashback to the restore point.  

    ```sql
    FLASHBACK DATABASE TO RESTORE POINT G202501011000;
    ```

####  Step 5: Revert High-Availability Environment Configuration

2. Start the target database node to NOMOUNT stage.

    ```shell
    $ yasboot node restart -c yashandb -n 1-3 -m nomount
    ```

2. Restore the REPLICATION_ADDR, ARCHIVE_DEST_n, and REQUIRED_SYNC_STANDBYS parameter configurations on the standby database to their original values.

3. Log in to the primary database as a DBA user, and restore the ARCHIVE_DEST_n, protection mode, or synchronized standby related configurations on the primary database.

4. Start standby database 1-3 normally. 

    At this point, the primary and standby databases will automatically synchronize data.

### Standby Cluster Flashback

####  Step 1: Select and Prepare the Target Standby Cluster

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the database's protection mode and primary-standby link configuration information.

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

3. Execute the following operations on the primary cluster to temporarily disassociate the primary cluster from all nodes of the target standby cluster.

    ```sql
    -- Clear the ARCHIVE_DEST_n parameters corresponding to the target standby cluster
    ALTER SYSTEM SET ARCHIVE_DEST_3 ='' SCOPE=BOTH;

    -- If the target standby cluster is explicitly designated as the synchronized standby, also modify the REQUIRED_SYNC_STANDBYS parameter accordingly
    ```

4. Connect and log in to an instance of the target cluster as a DBA user.
    ```shell
    $ yasql sales/********@192.168.10.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```

5. View the REPLICATION_ADDR, ARCHIVE_DEST_n, and REQUIRED_SYNC_STANDBYS parameter configurations, record the parameter values, and then clear all these parameters.

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

6. Log in to the other instances of this cluster in sequence and repeat the above operation to clear the corresponding parameters.  

    At this point it becomes equivalent to a single cluster.

####  Step 2: Complete Flashback Basic Configuration on the Target Cluster

1. Log in to the server where the instance 1 of the target cluster is located as the installation user.

2. Restart the target cluster to the MOUNT state.

    Only one instance needs to be in the MOUNT state while all other instances should be in the OFF state.

    ```shell
    # Stop all instances of the target cluster
    $ yasboot node stop -c yashandb -n 2-1
    $ yasboot node stop -c yashandb -n 2-2

    # Start one of the instances to the NOMOUNT stage
    $ yasboot node start -c yashandb -n 2-1 -m nomount

    # Log in to the current database instance as the sys user
    $ yasql sys/********@192.168.10.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 

    -- Start the database instance to the MOUNT stage
    SQL> ALTER DATABASE MOUNT;
    ```
    



4. Enable database flashback: 

    ```sql
    ALTER DATABASE FLASHBACK ON;
    ```

5. Create a permanent restore point.

    ```sql
    CREATE RESTORE POINT G202501011000 AS OF TIMESTAMP SYSTIMESTAMP() GUARANTEE FLASHBACK DATABASE;
    ```



5. Start the current instance to the OPEN stage.  

    ```sql
    ALTER DATABASE OPEN;
    ```


6. The target node executes failover.

    ```sql
    ALTER DATABASE FAILOVER;
    exit
    ```

7. Start other instances of the target cluster

    ```shell
    $ yasboot node start -c yashandb -n 2-2
    ```
    

####  Step 3: Execute Simulation Business

On the target cluster, execute the corresponding simulation business.

####  Step 4: Flashback Standby Cluster

1. Log in to the server hosting the target database node using the YashanDB installation user.


2. Restart the target cluster to the MOUNT state.

    Only one instance needs to be in the MOUNT state while all other instances should be in the OFF state.

    ```shell
    # Stop all instances of the target cluster
    $ yasboot node stop -c yashandb -n 2-1
    $ yasboot node stop -c yashandb -n 2-2

    # Start one of the instances to the NOMOUNT stage
    $ yasboot node start -c yashandb -n 2-1 -m nomount

    # Log in to the current database instance as the sys user
    $ yasql sys/********@192.168.10.4:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 

    -- Start the database instance to the MOUNT stage
    SQL> ALTER DATABASE MOUNT;
    ```
    

3. Perform flashback to the restore point.  

    ```sql
    FLASHBACK DATABASE TO RESTORE POINT G202501011000;
    ```

####  Step 5: Revert High-Availability Environment Configuration

1. Restart the current instance to the NOMOUNT stage.  

    ```shell
    SQL> exit

    # Start the current instance to the NOMOUNT stage
    $ yasboot node restart -c yashandb -n 2-1 -m nomount
    ```

2. Restore the ARCHIVE_DEST_n, REQUIRED_SYNC_STANDBYS, and REPLICATION_ADDR parameter configurations on the current instance to their original values.

3. Start other instances in the target cluster to the NOMOUNT stage in sequence, and restore their ARCHIVE_DEST_n, REQUIRED_SYNC_STANDBYS, and REPLICATION_ADDR parameter configurations to their original values.

4. Log in to each instance of the primary cluster as a DBA user, and restore the ARCHIVE_DEST_n and REQUIRED_SYNC_STANDBYS configurations on the primary cluster to their original values.

5. Start all instances of the standby cluster normally. 

    At this point, the primary and standby clusters will automatically synchronize data.
