## Overview

Snapshot Standby is a special standby mode provided by YashanDB. It is generated based on the instantaneous data image of a physical standby database in the current environment and is a lightweight data cloning capability provided by the database.

Snapshot Standby has the following characteristics:

- Continues to receive redo logs (stored in STANDBY LOG files) without affecting transaction commits on the primary database in maximum protection mode.

- Stops replaying redo logs; the data version is fixed and stable.

- Allows read and write operations, enabling temporary data queries, business verification, change validation, data rollback verification, offline statistical analysis, and test drills without affecting the primary database's business.

- Supports converting back to physical standby database at any time. The system will use flashback technology to restore the database to the point in time when it became a snapshot standby, automatically discarding all write operations during the snapshot standby's existence. The entire process will not cause data conflicts or data corruption. After the conversion is complete, the target standby database will restore standard physical standby capabilities and normally participate in primary-standby replication.

Snapshot Standby has the following operational requirements:

- Configure at least 3 STANDBY LOG files to receive all redo logs from the primary database during the snapshot standby period.

- Tablespace operations are not allowed, including adding/deleting tablespaces, modifying data files, etc.

- Online redo log management operations are not allowed, including adding, deleting, or clearing redo files, etc.

- Backup and recovery operations are not allowed.

## Typical Application Scenarios

Snapshot Standby is suitable for the following business scenarios:

  Execute complete business rehearsal scripts on the standby database to verify system performance after new feature deployment, without performing risky operations in the production environment.

  Perform SQL statement tuning, stored procedure debugging, or application compatibility verification in an isolated standby environment to avoid impacting core business operations.

  Quickly build test environments using existing standby resources without additional hardware procurement or complex data replication, significantly reducing test environment setup costs.

  Reproduce issues discovered in the production environment on the snapshot standby to facilitate root cause analysis and repair solution verification.

## Building a Snapshot Standby

Building a snapshot standby database refers to converting a physical standby database (not a cascade standby) with the role of STANDBY into a snapshot standby database.

### Preconditions

- Snapshot Standby is only applicable to Standalone Deployment.

- If the current environment is a standalone single database deployment, please refer to Standby Scaling to create a new standby database for the existing environment. At initialization, the standby database defaults to a physical standby database.

- The target standby database must be a physical standby database (the DATABASE_ROLE field in the V$DATABASE view is STANDBY).

- During the conversion, the target standby database needs to be restarted to the MOUNT stage.

- The file path, quantity, and storage space for STANDBY LOG files have been planned for the target standby database. STANDBY LOG files will be used to receive all redo logs from the primary database during the snapshot standby period. Please evaluate and plan the capacity based on daily business volume.

### Operation Steps

####  Step 1: Select the Target Standby Database

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```

2. View the database's primary-standby link configuration information.

    ```sql
    -- View ARCHIVE_DEST_n configuration  
    SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'ARCHIVE_DEST_%' AND VALUE IS NOT NULL;

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCHIVE_DEST_2                                                   SERVICE=192.168.1.3:1688 NODE_ID=1-2:2
    ARCHIVE_DEST_3                                                   SERVICE=192.168.1.4:1688 NODE_ID=1-3:3

    SELECT database_role, open_mode FROM v$database;

    DATABASE_ROLE        OPEN_MODE
    -------------------- -----------------
    STANDBY              READ_ONLY
    ```

    Select the target standby database to be built as a snapshot standby based on the configuration information (this document uses standby node 1-3 as an example).

3. Connect and log in to the target standby database as a DBA user.

4. Check and confirm its database role.

   ```sql
   SELECT database_role, open_mode FROM v$database;

   DATABASE_ROLE        OPEN_MODE
   -------------------- -----------------
   STANDBY              READ_ONLY
   ```
   If DATABASE_ROLE is not STANDBY, you need to select another standby database for subsequent operations.


#### Step 2: Execute conversion

1. Log in to the server where the target standby database is located as the installation user.

2. Restart the target standby database to MOUNT stage.

   ```shell
   $ yasboot node restart -c yashandb -n 1-3 -m mount
   ```

3. Connect and log in to the target standby database as the sys user.

   ```shell
   # Log in as sys user without password
   $ yasql / as sysdba

   # Or log in with password authentication
   $ yasql sys/********@192.168.1.4:1688
   ```

4. Convert the target standby database to a snapshot standby.

   ```sql
   ALTER DATABASE CONVERT TO SNAPSHOT STANDBY;
   ```

5. Create at least 3 STANDBY LOG files on the target standby database.

   ```sql
   ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/standby_redo1' SIZE 512M;
   ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/standby_redo2' SIZE 512M;
   ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/standby_redo3' SIZE 512M;
   ```

6. Open the target standby database.

   ```sql
   ALTER DATABASE OPEN;
   ```

7. Confirm the conversion result.

   ```sql
   SELECT database_role, open_mode FROM v$database;

   DATABASE_ROLE        OPEN_MODE
   -------------------- -----------------
   SNAPSHOT_STANDBY     READ_WRITE
   ```
   
   - DATABASE_ROLE = SNAPSHOT_STANDBY: Indicates that the snapshot standby conversion was successful. The current standby is in read-write mode and can normally execute read-write operations.

   - DATABASE_ROLE = SNAPSHOT_PREPARE: Indicates that the snapshot standby conversion is in progress, and the current standby is in the preparation phase. If the query result is still in this state, you can view the conversion progress through the V$RECOVERY_PROGRESS view.

      ```sql
      SELECT item, units, value FROM v$recovery_progress;
      ```

## Monitor Snapshot Standby Status

During the operation of the snapshot standby, the following key indicators need to be monitored:

- Storage space usage.

   Monitor the size of the tablespace during the operation of the snapshot standby:

   ```sql
   SELECT tablespace_name, SUM(bytes) / 1024 / 1024 AS size_mb
   FROM dba_data_files
   GROUP BY tablespace_name;
   TABLESPACE_NAME                                                      SIZE_MB 
   ---------------------------------------------------------------- ----------- 
   SYSTEM                                                                    64
   SYSAUX                                                                    64
   TEMP                                                                      64
   SWAP                                                                      64
   USERS                                                                     64
   UNDO                                                                     128
   ```

   If the table space usage is close to the threshold, you need to clear data or expand storage immediately.

- Standby redo log status.

   Monitor the usage of STANDBY LOG files to avoid frequent log file switching affecting performance:

   ```sql
   SELECT id, type, sequence#, block_count, used_blocks, status FROM v$logfile;

             ID TYPE         SEQUENCE#           BLOCK_COUNT           USED_BLOCKS STATUS
   ------------ --------- ------------ --------------------- --------------------- ---------
            0 ONLINE              65                 32768                 32768 INACTIVE
            1 ONLINE              66                 32768                 26090 CURRENT
            2 ONLINE              63                 32768                  6238 INACTIVE
            3 ONLINE              64                 32768                 32768 INACTIVE
          128 STANDBY              3                131072                   177 CURRENT
          129 STANDBY              0                131072                     0 NEW
          130 STANDBY              0                131072                     0 NEW
   ```

## Convert from Snapshot Standby to Physical Standby

To convert the snapshot standby back to physical standby, continuing to receive and apply redo logs from the primary database.

### Operation Scenarios

In the following scenarios, you need to convert the snapshot standby back to physical standby:

- After completing the business test, function verification, or problem reproduction on the snapshot standby, you need to restore the standby's primary-standby replication function.

- After the business needs to restore the data synchronization, ensure that the standby continues to receive and apply redo logs from the primary.

- The snapshot standby needs to continue to run as a formal standby of the production environment after completing the temporary task.

### Prerequisites 

- The target standby database must be a snapshot standby database (the DATABASE_ROLE field in the V$DATABASE view is SNAPSHOT_STANDBY).

- During the conversion, the target standby database needs to be restarted to the MOUNT stage.

- Archive logs: Ensure that primary database archive logs are complete, used for Flashback recovery during conversion.

### Operation Steps

1. Connect and log in to the target standby database as a DBA user.

2. Check and confirm its database role.

   ```sql
   SELECT database_role, open_mode FROM v$database;

   DATABASE_ROLE        OPEN_MODE
   -------------------- -----------------
   SNAPSHOT_STANDBY     READ_WRITE

   exit
   ```

3. Log in to the server where the target standby database is located as the installation user.

4. Restart the target standby database to MOUNT stage.

   ```shell
   $ yasboot node restart -c yashandb -n 1-3 -m mount
   ```

5. Connect and log in to the target standby database as the sys user.

   ```shell
   # Log in as sys user without password
   $ yasql / as sysdba

   # Or log in with password authentication
   $ yasql sys/********@192.168.1.4:1688
   ```

6. Convert the target standby database to a physical standby.

   ```sql
   ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
   ```

7. Open the target standby database to OPEN.

   ```sql
   ALTER DATABASE OPEN;
   ```

8. Confirm the conversion result.

   ```sql
   SELECT database_role, open_mode FROM v$database;

   DATABASE_ROLE        OPEN_MODE
   -------------------- -----------------
   STANDBY               READ_ONLY
   ```

   - DATABASE_ROLE = STANDBY: Indicates that the snapshot standby has successfully converted back to a physical standby. The current standby restores the original characteristics of the physical standby and continuously receives and replays redo logs from the primary database.

   - DATABASE_ROLE = SNAPSHOT_PREPARE: Indicates that the snapshot standby conversion is in progress, and the current standby is in the preparation phase. If the query result is still in this state, you can view the conversion progress through the V$RECOVERY_PROGRESS view.

      ```sql
      SELECT item, units, value FROM v$recovery_progress;
      ```
