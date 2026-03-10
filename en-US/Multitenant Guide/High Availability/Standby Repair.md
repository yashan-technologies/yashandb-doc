For certain specific exceptional scenarios, YashanDB provides quick repair functionality to ensure the smooth operation of a highly available environment. For syntax definitions and descriptions, please refer to [BUILD DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/BUILD DATABASE).

This chapter introduces the standby database repair solutions in Standalone Deployment.



When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the execution container for the BUILD DATABASE statement should be selected based on the object to be constructed:

- Connecting to the CDB root

    - Execute BUILD DATABASE: Construct the entire standby CDB (including the CDB root and all PDBs)

    - Execute BUILD DATABASE ROOT: Construct the standby CDB root

- Directly connecting to PDB and executing BUILD DATABASE: Only construct the corresponding standby PDB

  Additionally, standby PDB(s) construction can also be performed by connecting to the CDB root and executing [BUILD PLUGGABLE DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/BUILD PLUGGABLE DATABASE) for global operations.



## Standby Database has GAP

During high availability operations, the standby database may experience significant discrepancies from the primary database due to network transmission, disk I/O, and other reasons, leading to temporary inability to synchronize the primary database operations. In such scenarios, the `build database incremental` statement can be used to quickly repair the GAP, reducing the discrepancies between primary and standby, and allowing normal synchronization of primary database operations.

>  **Note**: 
>
>- The number of datafile, redofile, and bucket in the primary database and the standby database must be consistent in both count and version. The `build database incremental` statement cannot be used to repair the standby database GAP if they are inconsistent.
>- If the standby database state is "need repair," or the standby database is not open, the `build database incremental` statement cannot be used to repair the standby database GAP.
>- The `build database incremental` statement can also be used to repair gaps in cascade backups.
>- The primary database can repair the standby database GAP by specifying the IP address.
>- The `build database incremental` statement can only be executed within the same primary/standby or cascade backup cluster, and cannot cross-cluster to repair the GAP.

### Repair via Synchronous Backup

If synchronous backups have been configured in the high availability environment, or if there is a standby database with only a small gap from the primary, the standby database can be used to repair a much lagging standby database, minimizing the impact on the primary database and business operations.

***Example*** for Standalone Deployment

```sql
-- Check the status, database mode, network connection status, and IP address of the abnormal standby database and synchronous standby database on the primary database. STATUS should be NORMAL, CONNECTION should be CONNECTED, and DATABASE_MODE should be OPEN
SELECT CONNECTION,STATUS,DATABASE_MODE,PEER_ADDR FROM V$ARCHIVE_DEST_STATUS;

-- Use the synchronous backup to execute build database to repair the standby database GAP (standby_addr is the IP address of the abnormal standby database)
BUILD DATABASE INCREMENTAL TO REMOTE standby_addr;

-- Check the status, network connection, primary/standby gap, etc. of the standby database
SELECT * FROM V$REPLICATION_STATUS;
```

### Primary Database Repair

If there is no synchronous backup deployed in the high availability environment and all standby databases are significantly lagging behind the primary database, the gap must be repaired through the primary database.

***Example*** for Standalone Deployment

```sql
-- Check the status, database mode, and network connection status of the standby database. STATUS should be NORMAL, CONNECTION should be CONNECTED, and DATABASE_MODE should be OPEN
SELECT CONNECTION,STATUS,DATABASE_MODE,PEER_ADDR FROM V$ARCHIVE_DEST_STATUS;

-- Query the DB_UNIQUE_NAME of the standby database as standby_name
SELECT DB_UNIQUE_NAME FROM V$ARCHIVE_DEST;

-- Execute build database to repair the standby database GAP
BUILD DATABASE INCREMENTAL TO STANDBY standby_name;

-- Check the status, network connection, primary/standby gap, etc., of the standby database
SELECT * FROM V$REPLICATION_STATUS;
```

## Standby Database NEED REPAIR

During high availability operations, issues like deployment anomalies, user errors clearing primary database archives, or split-brain scenarios can lead to the standby database being unable to synchronize the primary database normally, resulting in the standby database being marked as NEED REPAIR. In this case, the abnormal standby database needs to be repaired.

The split-brain phenomenon occurs when multiple primary databases are providing services, causing potential data loss or inconsistency issues. For example, transactions committed on the old primary database may not exist on the new primary database, resulting in data loss. After demoting the old primary database, the redo logs do not match those of the new primary database, making it impossible to establish a normal primary/standby relationship.

### Quick Repair

In cases where the standby database cannot synchronize normally due to the reasons listed below, the `build database repair` statement can be used for a quick repair of the abnormal standby database:

- Primary database archives have been cleared
- Standby database archives are corrupted
- Split-brain situations

> **Note**: 
>
> - The number of datafile, redofile, and bucket in the primary database and the standby database must be consistent in both count and version. The `build database repair` statement cannot be used to repair the standby database if they are inconsistent.
> - If the standby database status is normal, or the standby database is not open, the `build database repair` statement cannot be used to repair the standby database.
> - The `build database repair` can also be used to repair cascade backups.
> - Split-brain situations may not successfully repair, and possible reasons for failure include: inability to find the log divergence point between the primary database and standby database, significant data differences leading to low repair efficiency, and failure.
> - Under distributed conditions, standby databases marked as "need repair" may cause normal cluster shutdowns via *yasboot* to stall; a forced restart of the cluster via *yasboot* is necessary.
> - YACs can only repair the need repair issue through full synchronization methods as listed below.

***Example*** for Standalone Deployment

```sql
-- Check the status, network connection status, and error reasons for need repair of the standby database. STATUS should be NEED REPAIR, CONNECTION should be CONNECTED
SELECT STATUS,CONNECTION,ERROR FROM V$REPLICATION_STATUS;

-- Query the DB_UNIQUE_NAME of the standby database as standby_name on the primary database
SELECT DB_UNIQUE_NAME FROM V$ARCHIVE_DEST;

-- Execute build database repair on the primary database to quickly repair the standby database
BUILD DATABASE REPAIR STANDBY standby_name;

-- Check the status, network connection, etc., of the standby database. STATUS should be NORMAL, CONNECTION should be CONNECTED
SELECT * FROM V$REPLICATION_STATUS;
```

### Full Synchronization

For any other reasons not listed for quick repair that lead to the standby database being unable to synchronize or unable to perform quick repair, full `build database` should be used to repair the standby database.

>  **Note**: 
>
>- If there are multiple standby databases marked as need repair, the `build database` command can be executed on the primary database to simultaneously repair multiple standby databases using `BUILD DATABASE TO STANDBY (standby1, standby2);` to improve repair efficiency.
>- When "need repair" occurs, the DBA must analyze the reasons for the standby database anomalies and choose the appropriate repair strategy. Quick repair solutions should be prioritized.

***Example*** for Standalone Deployment

```shell
-- Shut down the standby database
SQL> shutdown abort;

-- Clean up the data directory, local_fs directory, and subfiles and subdirectories under the archive directory
-- Restart the standby database to nomount

$ yasboot cluster start -c yashandb -m nomount
$ yasql / as sysdba

-- Check if the network is connected. If CONNECTION is CONNECTED, then the primary/standby network connection is normal. If DISCONNECTED, then the primary/standby network is not connected
SQL> SELECT CONNECTION,STATUS,PEER_ADDR,TRANSPORT_LAG,APPLY_LAG FROM V$REPLICATION_STATUS;
 
-- When the primary/standby network connection is normal, execute build database on the standby database
SQL> BUILD DATABASE;

-- Check the instance status of node_2. STATUS should be OPEN
SQL> SELECT STATUS FROM v$instance;

-- Check the status, open mode, and role information of node_2. STATUS should be NORMAL, OPEN_MODE should be READ_ONLY, DATABASE_ROLE should be STANDBY
SQL> SELECT STATUS, OPEN_MODE, DATABASE_ROLE FROM v$database;
```
<span id="buildpdb" name="buildpdb" class="yaslink"></span>

##  Other Standby Database Exception Recovery in CDB

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), standby PDB abnormal repair can be managed through unified operations via the CDB root, and if only the standby CDB root encounters abnormalities, there is no need to construct the entire CDB - only the standby CDB root needs to be constructed

### Batch Standby PDB Exception Recovery

When standby PDBs encounter abnormal situations, unified operations can also be performed through the CDB root by executing [BUILD PLUGGABLE DATABASE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/BUILD PLUGGABLE DATABASE) to quickly rebuild and recover single or multiple PDBs.

***Example*** for Standalone Deployment

```shell
# 1. Log in to the primary PDB1.
$ yasql sys/********@192.168.1.2:1688/pdb1

-- 2. Check the status, network connection status, and error reasons for need repair of the standby PDB. STATUS should be NEED REPAIR, CONNECTION should be CONNECTED
SQL> SELECT STATUS,CONNECTION,ERROR FROM V$REPLICATION_STATUS;
SQL> exit

# 3. Close the standby PDB1.
$ yasboot pdb stop -c yashandb -n 1-2 --pdb pdb1

# 4. Log in to the CDB root.
$ yasql sys/********@192.168.1.2:1688

-- 5. Clean up the data files of the target standby PDB1 (but PDB metadata must be retained).
SQL> DROP PLUGGABLE DATABASE pdb1 INCLUDING ARCHIVELOG KEEP METADATA;

-- 6. Execute BUILD PLUGGABLE DATABASE to build the target standby PDBs.
SQL> BUILD PLUGGABLE DATABASE pdb1;
SQL> exit

# 7. Log in to the primary PDB1
$ yasql sys/********@192.168.1.2:1688/pdb1

-- 8. Check the status, network connection, etc., of the standby PDB. STATUS should be NORMAL, CONNECTION should be CONNECTED
SELECT CONNECTION,STATUS,PEER_ROLE,PEER_MODE FROM V$REPLICATION_STATUS;
```

### The Standby CDB Root Exception Recovery

If the standby CDB root encounters abnormal conditions, the standby CDB root needs to be rebuilt.

***Example*** for Standalone Deployment

```shell
# 1. Log in to the server where the standby CDB root is located using the installation user.

# 2. Clean up the sub-files and sub-folders in the data directory, local_fs directory, and the archive directory of the standby CDB root.

# 3. Restart the standby CDB root to NOMOUNT stage.
$ yasboot node restart -c yashandb -n 1-2 -m nomount

# 4. Log in to the standby CDB root.
$ yasql / as sysdba

-- 5. Check the status, network connection status, and error reasons for need repair of the standby CDB root. STATUS should be NEED REPAIR, CONNECTION should be CONNECTED
SQL> SELECT STATUS,CONNECTION,ERROR FROM V$REPLICATION_STATUS;

-- 6. Execute build database root on the primary database to quickly repair the standby CDB root
SQL> BUILD DATABASE ROOT;

-- 7. Check the status, network connection, etc., of the standby CDB root. STATUS should be NORMAL, CONNECTION should be CONNECTED
SQL> SELECT CONNECTION,STATUS,PEER_ROLE,PEER_MODE FROM V$REPLICATION_STATUS;
```
