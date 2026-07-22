## General Description

ALTER DATABASE is used to modify the properties of a database.

In ISC Distributed Cluster Deployment, only the following syntax is supported:

- startup_clauses
- convert filename [including archivelog]
- archivelog/noarchivelog
- set standby database to
- convert to physical standby
- switchover
- failover
- exit upgrade
- add logfile
- drop logfile
- delete archivelog

It is recommended to use the *yasboot* operation and maintenance tool to manage distributed clusters. For details, refer to [yasboot command introduction](../../../Tools Guide/yasboot/Introduction to yasboot Command/00Introduction to yasboot Command).

Statement Definition
----

**alter database::=**

```ebnf
= ALTER DATABASE (startup_clauses|database_file_clauses|logfile_clauses|standby_database_clauses|upgrade_clauses|repair_database_clauses|delete_archivelog_clauses|double_write_file_clauses|supplemental_log_clauses|flashback_database_clauses|add_instance_clauses|remove_instance_clauses).
```

**[startup\_clauses](#startupclauses)::=**

```ebnf
= MOUNT|OPEN [READWRITE|RESETLOGS|UPGRADE|READONLY].
```

**[database\_file\_clauses](#databasefileclauses)::=**

```ebnf
= DATAFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause) | (OFFLINE [DROP])).

= TEMPFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause)).

= CONVERT FILENAME [INCLUDING ARCHIVELOG].
```

**[logfile_clauses](#logfileclauses)::=**

```ebnf
= (ARCHIVELOG|NOARCHIVELOG)|
    (SET STANDBY DATABASE TO MAXIMIZE (PERFORMANCE|PROTECTION|AVAILABILITY) [FORCE][TIMEOUT integer])|
    (ADD [STANDBY] LOGFILE [THREAD integer] '(' (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel]) {',' (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel])}')')|
    (DROP [STANDBY] LOGFILE [THREAD integer] filename)|(CLEAR [UNARCHIVED] LOGFILE filename).
```

**[standby\_database\_clauses](#standbydatabaseclauses)::=**

```ebnf
= CONVERT TO PHYSICAL STANDBY
|SWITCHOVER
|FAILOVER [RESET ID integer]
|(RECOVER ((MANAGED STANDBY DATABASE (([UNTIL SCN integer][DISCONNECT FROM SESSION])|CANCEL))|(TO LOGICAL STANDBY ((KEEP IDENTITY)|db_name))))
|(REGISTER [OR REPLACE] ARCHIVELOG {',' filename})
|(START LOGICAL STANDBY APPLY [IMMEDIATE])
|(STOP LOGICAL STANDBY APPLY).
```

**[upgrade\_clauses](#upgradeclauses)::=**

```ebnf
= EXIT UPGRADE.
```

**[repair\_database\_clauses](#repairdatabaseclauses)::=**

```ebnf
= CONVERT TO NORMAL.
```

**[delete\_archivelog\_clauses](#deletearchivelogclauses)::=**

```ebnf
= DELETE ARCHIVELOG (ALL|UNTIL ((SEQUENCE integer [THREAD integer])|TIME date|SCN integer))[FORCE].
```

**[double\_write\_file\_clauses](#doublewritefileclauses)::=**

```ebnf
= DOUBLE_WRITE RESIZE FILE size_clauses.
```

**[supplemental\_log\_clauses](#supplementallogclauses)::=**

```ebnf
= (ADD|DROP) SUPPLEMENTAL LOG (DATA ['(' (ALL|PRIMARY KEY) {',' (ALL|PRIMARY KEY) }')' COLUMNS] | TABLE TYPE '('(HEAP|TAC|LSC) {',' ( HEAP|TAC|LSC)}')').
```

**[flashback\_database\_clauses](#flashbackdatabaseclauses)::=**

```ebnf
= FLASHBACK (ON|OFF).
```

**[add\_instance\_clauses](#addinstanceclauses)::=**

```ebnf
= ADD INSTANCE [ "(" (logfiles_clause | UNDO TABLESPACE DATAFILE datafiles_clause) ")" ].
```

**[remove\_instance\_clauses](#removeinstanceclauses)::=**

```ebnf
= REMOVE INSTANCE (instance_id).
```

<span id="startupclauses" name="startupclauses"></span>

### startup\_clauses

This statement is used to MOUNT and OPEN the database for user access.

MOUNT is the state after the database mounts physical files, while OPEN is the state after the database is opened. For detailed database startup information, please refer to the [Instance Startup and Shutdown](../../../Database Administration/Instance Management/Instance Startup and Shutdown).



When the database instance moves from the NOMOUNT or MOUNT stage to the OPEN stage, it supports four open modes: READWRITE, READONLY, RESETLOGS, and UPGRADE.

- **READWRITE**: The database defaults to opening in READWRITE mode. In this mode, the database supports full transactional read/write operations and is used in formal production environments.

- **READONLY**: The database is opened in read-only mode, restricting the database to read-only operations without generating any redo. 
  
  - In YAC Deployment, the database cannot be opened in read-only mode.

  - In Standalone Primary/Standby Deployment, the physical standby database opens in this mode by default.

- **UPGRADE**: The upgrade tool *yasboot* opens the database in this mode during the upgrade process. In this mode, new session connections are not allowed, and manual OPEN operations are also not permitted.

- **RESETLOGS**: When a PITR (Point In Time Recovery), database flashback, or logical standby database configuration has occurred, and if a complete recovery cannot be performed, the database must be opened in RESETLOGS mode. This mode will reset the redo log sequence number.



***Example***

```sql
ALTER DATABASE MOUNT;
 
ALTER DATABASE OPEN;

ALTER DATABASE OPEN READWRITE;
```

<span id="databasefileclauses" name="databasefileclauses"></span>

### database\_file\_clauses

This statement is used to control the automatic extension of data files and to specify sizes. 

This action requires the database to be in OPEN state. When setting data file auto-extend or RESIZE data file, use the tempfile option for TEMP tablespaces and SWAP tablespaces, while other tablespaces should use the datafile option.

#### AUTOEXTEND OFF

Disables the auto-extension for a specific data file, and the NEXT_SIZE and MAX_SIZE of that data file are set to 0.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND OFF;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND OFF;
```

#### AUTOEXTEND ON

Enables auto-extension for a specific data file.

* NEXT size_clause: Specifies the size of each automatic extension for the data file, in Bytes. The value range is [512,32768] BLOCK sizes. If omitted, the default is 8M BLOCK sizes.

* MAXSIZE UNLIMITED/size_clause: Specifies the maximum capacity that the data file can expand to, in Bytes. UNLIMITED means unlimited. If omitted, the default is 64M BLOCK sizes.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
```

#### RESIZE

Re-specifies the size of a specific data file.

This functionality is only applicable to Standalone Deployment.

When creating data files under tablespaces, setting the auto-extend can help meet user requirements for larger data files. However, if storage space is limited, or if a data file has been over-specified in size but utilizes very little, or if the data file contains a large amount of deleted temporary data that has not been reclaimed, it would be necessary to use resize to shrink the data file and free up disk space.

When resizing the data file to be larger, set the file to your desired size to avoid frequent resource requests.

The size after resizing must be between 128 blocks to 64MB.

A resize operation may not always succeed; for instance, trying to increase its size may result in insufficient disk space, or when decreasing its size, the current valid data size of the file may exceed the specified value.

The data file in the UNDO tablespace can only be increased, not decreased.

In YAC/Distributed Cluster Deployment, local TEMP/SWAP tablespace data file resizing cannot be performed across instances, i.e., only resize local files on the server hosting the current instance.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' RESIZE 1048576;
ALTER DATABASE TEMPFILE '?/dbfiles/temp' RESIZE 1048576;
```

<span id="datafileoffline" name="datafileoffline"></span>

#### OFFLINE [DROP]

Immediately take a data file in a user-created tablespace offline (without waiting for data to be flushed to disk). After the data file is offline, it cannot be read from or written to, and operations such as resizing the file or setting the auto - extend switch cannot be performed on it.

Taking a data file offline will also take the tablespace it belongs to offline. All data files within this tablespace will be set to the RECOVER state, which has the same effect as executing [OFFLINE IMMEDIATE](./ALTER TABLESPACE.md#offline_option) on the tablespace.

The usage rules for the data file offline functionality are as follows:

- In YAC/Distributed Cluster Deployment, this functionality is not available.

- This operation should be carried out when the database is in the OPEN state (recommended) or during the MOUNT stage. If the operation is performed during the MOUNT stage, the following additional points should be noted:

    - In some cases (for example, when the redo log for creating the tablespace to which the file belongs is replayed during the startup process), the database will repair the data file that has been taken offline by this operation and its associated tablespace, and set them to the ONLINE state.
    
    - After performing this operation and then opening the database, querying V$DATAFILE will not be able to display the creation time of this file normally, unless it is brought back online again.

- In primary-standby HA deployment, it is recommended to perform this operation on the primary database. The same file on the standby database will be taken offline through the synchronization of the redo log. If this operation needs to be performed on the standby database, it can only be done during the MOUNT stage. After the standby database is started, it will be in the need - repair state.

- Only one data file can be specified per operation. Also, operations on data files of built-in tablespaces and on shadow files are not allowed.

- An offline tablespace and its data files remain part of the database. Therefore, it is not possible to create a tablespace or data file with the same name.

This operation is generally applied in the following business scenarios:

- When the database cannot be opened due to data file corruption or loss, during the MOUNT stage, you can first take the target file offline and then start the database to the OPEN state.

- When data isolation is required, you can temporarily take a certain data file offline.

- When the database is in an ABNORMAL state due to data file corruption (which can be checked through the ERROR_COMMENTS field in the V$DIAG_INCIDENT view), you can first take the target file offline and then repair the database state.

##### DROP

When the database is not in archiving mode, executing data file offline must specify this option. If archiving mode is enabled, this option is ignored, meaning OFFLINE DROP = OFFLINE.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE;

-- In non-archiving mode, the following command is the only way to take the data file offline
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE DROP;
```

#### CONVERT FILENAME [INCLUDING ARCHIVELOG]

When the entire database is migrated to a different directory, this syntax can be used to convert the paths recorded in the control files to allow the database to continue to start and operate normally. This statement is only used when the instance is in NOMOUNT stage.

The specific steps for using this statement are as follows:

1. Update the startup path: Modify the CONTROL_FILES parameter in the configuration parameter file to the current database startup path, or remove the existing configuration to use the default configuration for startup.
2. (Optional) If you need to update the storage path of archive log files: Modify the ARCHIVE_LOCAL_DEST parameter in the configuration parameter file to set the current archive path for the database, or remove the existing configuration to use the default storage path for archive log files.
3. Configure the data file path conversion parameter DB_FILE_NAME_CONVERT to convert the old file paths to the new ones. At the same time, confirm whether dual-write is needed; if dual-write is required, ensure that the data file conversion path parameters can apply to the dual-write file paths.
4. Configure the online log file path conversion parameter REDO_FILE_NAME_CONVERT to convert the old file paths to the new ones.
5. Configure the DATABUCKET path conversion parameter DB_BUCKET_NAME_CONVERT to convert the old file paths to the new ones.
6. Start the database to NOMOUNT state, and depending on whether it is necessary to convert archive log files, decide whether to explicitly specify the INCLUDING ARCHIVELOG clause.
7. Start the database to OPEN state.

##### INCLUDING ARCHIVELOG

Specify this clause when you need to convert archive log files' paths.

***Example***

```sql
ALTER DATABASE CONVERT FILENAME;

-- Path conversion with the inclusion of archive log files is done with the following statement
ALTER DATABASE CONVERT FILENAME INCLUDING ARCHIVELOG;
```

<span id="logfileclauses" name="logfileclauses"></span>

### logfile\_clauses

This statement is used to set the archiving mode of the database's redo logs, configure the protection mode of the standby database, add, delete, etc.

#### ARCHIVELOG/NOARCHIVELOG

Enables or stops the database's log archiving mode.

In Standalone Deployment, this operation requires the database instance to be in MOUNT state.

In YAC/Distributed Cluster Deployment, this operation requires the current instance to be in MOUNT state while other instances are in NOMOUNT state.

When the database is in replication mode (primary/standby standalone deployment or primary/standby cluster deployment), it is not possible to switch from archiving mode to non-archiving mode.

***Example***

```sql
ALTER DATABASE ARCHIVELOG;
 
ALTER DATABASE NOARCHIVELOG;
```

<span id="set_standby_clause" name="set_standby_clause"></span>

#### SET STANDBY DATABASE TO

Specifies the protection mode of the standby database, with the default value being MAXIMIZE PERFORMANCE. The protection modes are as follows:

* MAXIMIZE PERFORMANCE: In this mode, the primary database transactions can be committed without waiting for the standby database to receive the logs, ensuring the availability and performance of the primary database, but data may be lost if the primary database fails.

* MAXIMIZE PROTECTION: In this mode, the data protection of the standby database takes priority over the availability of the primary database. By default, transactions on the primary database are only committed after the logs are synchronized with the standby database and written to disk (if COMMIT_WAIT = NOWAIT, then primary database transactions can commit without waiting for the logs to be written). If the synchronized standby database fails, the primary database will switch to read-only mode after some time.

* MAXIMIZE AVAILABILITY: In this mode, when the synchronized standby database is normal, by default transactions on the primary database can only be committed after the logs are written to the synchronized standby database (if COMMIT_WAIT = NOWAIT, then primary database transactions can commit without waiting for the logs to be written); when the synchronized standby database fails, transactions will not be blocked, ensuring database availability.

> **Note**: 
>
> In YAC/Distributed Cluster Deployment, setting maximize protection mode requires all active instances to be in OPEN state.
>
> In YAC/Distributed Cluster Deployment, the TIMEOUT field cannot be used.

**FORCE**

For switching to MAXIMIZE PROTECTION mode, the logs from the primary database must have been synchronized to the standby database; otherwise, an error will be raised. Specifying the FORCE keyword means forcefully setting, ignoring this error.

**TIMEOUT**

The TIMEOUT keyword specifies the time to wait for the standby database to synchronize when setting maximize protection mode. If this time is exceeded, an error will be raised, with the unit in seconds, which can be omitted with the default being 10 seconds.

***Example***

```sql
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PERFORMANCE;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE AVAILABILITY;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION FORCE;
```

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION TIMEOUT 100;
```

<span id="add_logfile" name="add_logfile"></span>

#### ADD LOGFILE

Adds a new redo log to the database while allowing multiple files to be added separated by commas. This operation requires the database to be in OPEN state.

In YAC/Distributed Cluster Deployment, redo log files are dedicated to each instance. Each instance can add redo log files to its own set, while the master instance can add redo log files for offline instances by specifying THREAD integer, where integer represents the instance ID.

##### STANDBY

Specifying the STANDBY clause adds redo log files to the standby redo log. If this field is not specified, one redo file will be added to the online redo log.

##### filename

Specifies the filename of the newly added redo log file.

##### SIZE

Specifies the size of the newly added redo log file.

The minimum size of the redo log file is influenced by the parameters DB_BLOCK_SIZE, MAX_SESSIONS, and REDO_BUFFER_SIZE. The minimum value can be referenced with the formula: `DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2`.

##### BLOCKSIZE

The block size of the redo log file, with a default of 4096, can be manually specified as an integer multiple of 512. The actual size of the created file will be a multiple of BLOCKSIZE; if the specified size is not a multiple, it will be rounded up to the nearest multiple.

##### PARALLEL

Specifies the degree of parallelism for creating the redo log file, with a range from 1 to 8. If not specified, the system dynamically adjusts the degree of parallelism based on the file size; for example, it will be 1 if the file is not larger than 1G, 8 if over 128G, and 4 if between 1G and 128G.

***Example***

```sql
ALTER DATABASE ADD LOGFILE ('?/dbfiles/redo5' SIZE 72355840,'?/dbfiles/redo6' SIZE 72355840);
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo6' SIZE 72355840 BLOCKSIZE 512;
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo7' SIZE 72355840 PARALLEL 4;
ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/redo8' SIZE 72355840 BLOCKSIZE 512;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
-- Assuming instance 2 has left the cluster, the master instance can add redo log files for instance 2
ALTER DATABASE ADD LOGFILE THREAD 2 '?/dbfiles/redo27' SIZE 72355840 PARALLEL 4;

ALTER DATABASE ADD STANDBY LOGFILE THREAD 2 '?/dbfiles/redo28' SIZE 72355840 BLOCKSIZE 512;
```

<span id="drop_logfile" name="drop_logfile"></span>

#### DROP LOGFILE

Deletes an existing redo log. It is not permitted to delete a redo log that is currently in use. This operation requires the database to be in OPEN state.

In YAC/Distributed Cluster Deployment, redo log files are dedicated to each instance. Each instance can remove its own redo log files, while the master instance can delete redo log files for offline instances by specifying THREAD integer, where integer represents the instance ID.

##### STANDBY

Specifying the STANDBY clause deletes a standby redo log. If this field is not specified, it deletes an online redo log.

***Example***

```sql
ALTER DATABASE DROP LOGFILE '?/dbfiles/redo5';
ALTER DATABASE DROP STANDBY LOGFILE '?/dbfiles/redo8';
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
-- Assuming instance 2 has left the cluster, the master instance can delete redo log files for instance 2
ALTER DATABASE DROP LOGFILE THREAD 2 '?/dbfiles/redo27';

ALTER DATABASE DROP STANDBY LOGFILE THREAD 2 '?/dbfiles/redo28';
```

#### CLEAR LOGFILE

Reinitializes the online redo file. If the redo file does not exist or has been mistakenly deleted, this statement will recreate that file, commonly used for handling scenarios such as damaged or missing redo files.

##### UNARCHIVED

The redo file is reinitialized without waiting for archiving.

> **Caution**: 
> 
> Specifying the UNARCHIVED keyword carries the following risks and should be used with caution.
> - May render old backup sets unusable; it is advisable to perform a backup.
> - May cause the standby database to become unusable; subsequent recovery can be done through building the database. Refer to [Repairing Abnormal Standby Database](../../../High Availability/Standby Repair).

***Example***

```sql
ALTER DATABASE CLEAR LOGFILE '?/dbfiles/redo5';
```

<span id="standbydatabaseclauses" name="standbydatabaseclauses"></span>

### standby\_database\_clauses

This statement is used to switch between the primary and standby databases. For detailed operations of primary and standby databases, refer to the [High Availability](../../../High Availability/Overview of YashanDB High Availability).

#### CONVERT TO PHYSICAL STANDBY

Switches from the primary database to the standby database.

***Example***

```sql
ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
```

> **Note**: 
>
> - The role of the database must be PRIMARY, and the executing instance must be in MOUNT state.
> - In YAC/Distributed Cluster Deployment, only instance 1 and if that instance is MASTER_ROLE can perform this operation.

#### SWITCHOVER

Switches from the standby database back to the primary database.

***Example***

```sql
ALTER DATABASE SWITCHOVER;
```

> **Note**: 
>
> - The role of the database must be STANDBY, and the executing instance must be in OPEN state.
> - All active instances of the primary database must be in OPEN state.
> - The redo transmission link between primary and standby must be functioning normally, and the standby database must be in NORMAL state. (The primary database checks the view V$ARCHIVE_DEST_STATUS; the standby database checks the view V$REPLICATION_STATUS.)
> - In YAC/Distributed Cluster Deployment, the primary database's instance 1 must be alive.
> - In YAC/Distributed Cluster Deployment, switchover performance is affected by checkpoints, waiting for non-instance 1 to complete full checkpoints before performing this operation.

<span id="failover" name="failover"></span>

#### FAILOVER

When the primary database has failed and cannot be recovered, forcefully switches the standby database to become the primary database.

***Example***

```sql
ALTER DATABASE FAILOVER;
```

> **Note**: 
>
> - The role of the database must be STANDBY.
> - The executing instance must be in OPEN state.
> - The database connection to the primary database must be disconnected. You can check the connection status of primary/standby using the view V$REPLICATION_STATUS.
> - In YAC/Distributed Cluster Deployment, this operation must be executed on the master instance of the standby cluster. Other surviving instances of the standby cluster should reprimary in OPEN state.
> - RESET ID is only used in automatic failover statements issued by yasom in election scenarios; **do not manually specify this option**.

#### RECOVER MANAGED STANDBY DATABASE CANCEL

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to stop the current apply operation. 

If a foreground apply is in process, this thread will be interrupted and exited.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
```

#### RECOVER MANAGED STANDBY DATABASE

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to start the standby database apply; to exit, it is necessary to execute the cancel apply SQL statement.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
```

##### UNTIL SCN integer

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to start the standby database apply and to exit when reaching the specified SCN. 

At this point, to exit, it is necessary to execute the cancel apply SQL statement.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123;
```

##### DISCONNECT FROM SESSION

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to apply in the background, allowing the current session to perform other tasks.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123 DISCONNECT FROM SESSION;
```

#### TO LOGICAL STANDBY

This statement is used to convert a physical standby database into a logical standby database.

##### KEEP IDENTITY

If you want to use the rolling upgrade functionality provided by the logical standby database and return to the original configuration of the primary database and physical standby database, please use this clause.
> **Note**: 
>
> This clause is only used in rolling upgrade scenarios and should not be used to create a general logical standby database.

##### db_name

Specifies the database name to identify the new logical standby database. 

After constructing the logical standby database in this way, it is necessary to perform the ALTER DATABASE OPEN RESETLOGS operation.

#### REGISTER ARCHIVELOG

This statement is used to manually register archiving. The functionality constraints of this SQL include:

* After RESTORE DATABASE and when the database is not open, this SQL can be used to manually register archiving.
* After recovery or creation of completeness, the operation object must be the standby database, and the configuration parameter SANDBOX_STANDBY must be TRUE.
* The specified archive path can be an absolute path or a filename. When using a filename, the default path is the archive path (configuration parameter ARCHIVE_LOCAL_DEST).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE REGISTER ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC';
ALTER DATABASE REGISTER ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC', '/home/yashan/archive/arch_0_2.ARC';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';

-- Used in conjunction with RESTORE DATABASE
RESTORE DATABASE FROM 'BAK1';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';
RECOVER DATABASE;
ALTER DATABASE OPEN;
```

#### OR REPLACE

If an archive is found to be already registered, replace the original registered archive. This operation is risky and should be used with caution.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC', '/home/yashan/archive/arch_0_2.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';
```

#### START LOGICAL STANDBY APPLY

This statement is used to enable the logical apply functionality for the logical standby database, which is off by default.

#### STOP LOGICAL STANDBY APPLY

This statement is used to orderly stop the log apply service of the logical standby database.

It is not applicable to physical standby databases.

<span id="upgradeclauses" name="upgradeclauses"></span>

### upgrade\_clauses

This statement is used for database version upgrades.

#### EXIT UPGRADE

After the database upgrade is complete, you can directly exit the upgrade mode into normal OPEN mode without restarting.

***Example***

```sql
ALTER DATABASE EXIT UPGRADE;
```

<span id="repairdatabaseclauses" name="repairdatabaseclauses"></span>

### repair\_database\_clauses

This statement is used when the database is in an ABNORMAL state and requires DBA intervention for repair.

#### CONVERT TO NORMAL

When the database encounters a failure and is set to read-only, the database is in a fault state. After DBA repairs, this statement can be used to manually switch the database to normal mode.

> **Note**: 
>
> This statement cannot be used to set the database status to normal when the database is in an abnormal state due to resource errors.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE CONVERT TO NORMAL;
```

<span id="deletearchivelogclauses" name="deletearchivelogclauses"></span>

### delete\_archivelog\_clauses

This statement is used to manually clean up the archiving files of the database to free up disk space.

The conditions for manually cleaning up archive log files are determined by the ARCHIVELOG_DELETION_POLICY or ARCH_CLEAN_IGNORE_MODE parameters. For specific descriptions of the cleanup conditions, refer to [Archive Management](../../../Database Administration/Instance Management/Archive Management).

#### DELETE ARCHIVELOG 

##### ALL

Cleans up all archives that meet the cleanup conditions.

##### UNTIL SEQUENCE integer [THREAD integer]

Cleans up archives that meet cleanup conditions prior to the specified sequence number. If no instance is specified, the default will remove archives for instance 1.

##### UNTIL TIME date

Cleans up archives that were generated prior to the specified time and meet cleanup conditions.

##### UNTIL SCN integer

Cleans up archives that were generated prior to the specified SCN and meet cleanup conditions. (Comparing with NEXT_CHANGE# in V$ARCHIVED_LOG).

###### FORCE 

Forces the cleanup of archives without considering cleanup conditions.

***Example***

```sql
ALTER DATABASE DELETE ARCHIVELOG ALL;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME to_date('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss');

-- Forceful archive cleanup
ALTER DATABASE DELETE ARCHIVELOG ALL FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5 FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME to_date('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss') FORCE;
```

<span id="doublewritefileclauses" name="doublewritefileclauses"></span>

### double\_write\_file\_clauses

This statement is used to re-specify the size of the double write file.

In YAC/Distributed Cluster Deployment, there is no double write file.

In ISC Distributed Cluster Deployment, it is not allowed to adjust the size of the double write file.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DOUBLE_WRITE RESIZE FILE 32M;
```

<span id="supplementallogclauses" name="supplementallogclauses"></span>

### supplemental\_log\_clauses

This statement is used for configuring database-level supplemental logging, which is not applicable to ISC Distributed Cluster Deployment.

When supplemental logging is enabled, the database will additionally record some data in redo, including the original SQL text of DDL, and index information used for locating rows during update and delete, etc.

With supplemental logging, corresponding DDL and DML statements can be restored through redo parsing, typically used for heterogeneous database synchronization.

Database-level supplemental logging takes effect on all selected types of user tables, and the effective state can be viewed through the dynamic view [V$DATABASE](../../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE). If the target table has [encryption](CREATE TABLE.md#columnencryptionclause) enabled, the database-level supplemental logging will still display the effective status based on the selected table types, but relevant supplemental information will not be recorded in redo.

For table-level supplemental logging, please refer to [ALTER TABLE](ALTER TABLE.md#addsupplementalloggingclause).

If you wish to use the [YStream server](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM), you must first enable supplemental logging (either at the library or table level) before creating the YStream server; otherwise, it will result in an error.

#### SUPPLEMENTAL LOG DATA

SUPPLEMENTAL LOG DATA indicates minimal supplemental logging. In this mode, the redo will additionally record the DDL text and DML rowid, with minimal performance impact.

When enabling supplemental logging in ALL or PRIMARY KEY mode, minimal supplemental logging is implicitly enabled. Additionally, it cannot be turned off before disabling other modes of supplemental logging.

The priority of ALL mode is higher than PRIMARY KEY mode; when either database-level or table-level supplemental logging mode is set to ALL, that table will use ALL mode.

Database-level supplemental logging records DDLs for objects such as FUNCTION, PACKAGE, PROCEDURE, SEQUENCE, TRIGGER, SYNONYM, LIBRARY, TABLE, INDEX, TYPE, VIEW, and MATERIALIZED VIEW, but will not record DDLs that do not modify metadata.

##### ALL

In ALL mode, the redo will additionally record DDL text and DML rowid, and will also record all columns in the original row during updates and deletes (except LOBs and VARCHARs and CHARs exceeding 32K).

This mode is suited for tables without primary keys, as it adds more data in redo, which has a higher performance impact.

##### PRIMARY KEY

In PRIMARY KEY mode, the redo will additionally record DDL text and DML rowid and will try to record primary key columns during updates and deletes.

If the table does not have a primary key, but has a non-null unique index, it will record that indexed column.

If the table has neither a primary key nor a non-null unique index, it will record all columns in the original row (except LOBs, and VARCHARs and CHARs exceeding 32K).

In this mode, priority is given to recording the primary key, which reduces redo size and has a lower performance impact.

***Example*** for Standalone Deployment
```sql
-- Enable minimal supplemental logging
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;

-- Enable PRIMARY KEY mode supplemental logging
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

-- Enable ALL mode supplemental logging
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

-- Disable ALL and PRIMARY KEY mode supplemental logging
ALTER DATABASE DROP SUPPLEMENTAL LOG DATA (ALL, PRIMARY KEY) COLUMNS;

-- Disable minimal supplemental logging
ALTER DATABASE DROP SUPPLEMENTAL LOG DATA;
```

#### SUPPLEMENTAL LOG TABLE TYPE

Sets the table types which the database-level DML supplemental logging applies to. It is empty by default, and after enabling supplemental logging, please simultaneously set the table types to be affected.

For LSC tables, the rowids of hot data may change after transitioning from cold data and thus, it is not recommended to use rowids as the row locator information for LSC tables.
LSC tables do not generate redo for inserts when imported or created via create table as select.

***Example*** for Standalone Deployment
```sql
-- Set database-level supplemental logging to apply to HEAP and TAC type tables
ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP, TAC);

-- Set database-level supplemental logging to not apply to LSC tables
ALTER DATABASE DROP SUPPLEMENTAL LOG TABLE TYPE (LSC);
```

> **Note**: 
>
> Changing the status of database-level supplemental logging will not take effect on currently executing statements.

<span id="flashbackdatabaseclauses" name="flashbackdatabaseclauses"></span>

### flashback\_database\_clauses

This statement is used to configure the full flashback functionality for the database. 

After enabling database flashback on the primary node, the database allows flashing back to a specified point in time without backup sets.

In high-availability deployment scenarios, full database flashback is primarily enabled and executed on the primary database. For standby databases, it is currently applicable only in specific fault recovery scenarios. For more details, please refer to [Full Database Flashback](../../../Database Administration/Fault Handling/Flashback/Database Flashback/00Database Flashback).  

The full flashback functionality must adhere to the following rules:

- This statement is not applicable to ISC Distributed Cluster Deployment.

- The database must have archiving mode enabled.

- The executing instance must be in MOUNT or OPEN state.

- In YAC/Distributed Cluster Deployment, full database flashback operations can only be performed on the master instance (INSTANCE_ROLE = MASTER_ROLE in the [GV$INSTANCE](../../../Reference Manual/System Views/Dynamic Performance Views/GV$INSTANCE) view).  

- Normally, the full database Flashback feature cannot be disabled when [permanent restore points](CREATE RESTORE POINT) exist. You must first manually [delete](DROP RESTORE POINT) all permanent restore points.

- When performing a SWITCHOVER, the Flashback feature on the old primary will be forcibly and automatically disabled (even if permanent restore points exist).

- When disabling the full flashback functionality, all common (non-permanent) restore points will be deleted.

> **Note**: 
>
> Enabling database flashback may impact database write performance (estimated to reduce by approximately 8%).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE FLASHBACK ON;
```

<span id="addinstanceclauses" name="addinstanceclauses"></span>

### add\_instance\_clauses

This statement is used to add a new database instance to a YAC. The syntax and rules of logfiles_clause, datafiles_clause, and others are the same as those in the corresponding clauses of [CREATE DATABASE](CREATE DATABASE).

> **Caution**:
>
> For [YAC instance expansion](../../../Installation and Upgrade/Scalability/Scaling for YAC Deployment/Cluster Instance Scaling), it is recommended to use the *yasboot* tool for a one-click command. **It is not recommended** to execute this statement separately to add database instances.

The execution of this statement must follow the rules below:

- It is applicable only to YAC/Distributed Cluster Deployment.
- It can only be executed on the master instance (i.e., the instance where the INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE), and the master instance must be in OPEN state. If in Primary-Standby YAC Deployment environment, this operation can only be performed on the master instance of the primary cluster.
- This statement cannot be executed concurrently with ALTER DATABASE's add_instance_clauses, switchover, data file add/delete/resize, redo file add/delete, YStream creation/running, distributed transactions, backup recovery, and other operations. It must wait for any such event to complete before proceeding to the next operation.
- During the execution of this statement, no other instances are allowed to change from nomount to mount/open state.
- A single execution of this statement can only add one instance; if needed, the statement can be executed repeatedly.
- After adding an instance, the total number of instances must not exceed the current cluster's [MAXINSTANCES](CREATE DATABASE.md#maxinstances) value.

<span id="removeinstanceclauses" name="removeinstanceclauses"></span>

### remove\_instance\_clauses

This statement is used to add a new database instance to a YAC. The instance id in the statement can be obtained by viewing the [GV$INSTANCE](../../../Reference Manual/System Views/Dynamic Performance Views/GV$INSTANCE) view.

> **Caution**:
>
> For [YAC instance expansion](../../../Installation and Upgrade/Scalability/Scaling for YAC Deployment/Cluster Instance Scaling), it is recommended to use the *yasboot* tool for a one-click command. **It is not recommended** to execute this statement separately to delete database instances.

The execution of this statement must follow the rules below:

- It is applicable only to YAC/Distributed Cluster Deployment.
- The instance must be shut down prior to deletion. No mandatory shutdown instance deletion in Primary-Standby YAC Deployment.
- It can only be executed on the master instance (i.e., the instance where the INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE), and the master instance must be in OPEN state. If in Primary-Standby YAC Deployment environment, this operation can only be performed on the master instance of the primary cluster.
- This statement cannot be executed concurrently with ALTER DATABASE's add_instance_clauses, switchover, data file add/delete/resize, redo file add/delete, ystream creation/running, distributed transactions, backup recovery, and other operations. It must wait for any such event to complete before proceeding to the next operation.
- No other instances (including those starting to mount/open stage) may join the cluster while this statement is running.
- A single execution of this statement can only delete one instance; if needed, the statement can be executed repeatedly.
