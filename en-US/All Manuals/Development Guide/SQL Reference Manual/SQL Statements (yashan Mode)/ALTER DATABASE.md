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

It is recommended to use the *yasboot* operation and maintenance tool to manage distributed clusters. For details, refer to the [yasboot command introduction](../../../Tools Guide/yasboot/Introduction to yasboot Command/00Introduction to yasboot Command) section.

Statement Definition
----

**alter database::=**

```ebnf+diagram
syntax::= ALTER DATABASE (startup_clauses|database_file_clauses|logfile_clauses|standby_database_clauses|upgrade_clauses|repair_database_clauses|delete_archivelog_clauses|double_write_file_clauses|supplemental_log_clauses|flashback_database_clauses|add_instance_clauses)
```

**[startup\_clauses](#startupclauses)::=**

```ebnf+diagram
syntax::= MOUNT|OPEN [READWRITE|RESETLOGS|UPGRADE|READONLY]
```

**[database\_file\_clauses](#databasefileclauses)::=**

```ebnf+diagram
syntax::= DATAFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause) | (OFFLINE [DROP]))

syntax::= TEMPFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause))

syntax::= CONVERT FILENAME [INCLUDING ARCHIVELOG]
```

**[logfile\_clauses](#logfileclauses)::=**

```ebnf+diagram
syntax::= (ARCHIVELOG|NOARCHIVELOG)|
    (SET STANDBY DATABASE TO MAXIMIZE (PERFORMANCE|PROTECTION|AVAILABILITY) [FORCE][TIMEOUT integer])|
    (ADD [STANDBY] LOGFILE "(" (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel]) {"," (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel])}")")|
    (DROP [STANDBY] LOGFILE filename)|(CLEAR [UNARCHIVED] LOGFILE filename)
```

**[standby\_database\_clauses](#standbydatabaseclauses)::=**

```ebnf+diagram
syntax::= CONVERT TO PHYSICAL STANDBY
|SWITCHOVER
|FAILOVER (RESET ID integer)
|(RECOVER ((MANAGED STANDBY DATABASE (([UNTIL SCN integer][DISCONNECT FROM SESSION])|CANCEL))|(TO LOGICAL STANDBY ((KEEP IDENTITY)|db_name))))
|(REGISTER [OR REPLACE] ARCHIVELOG {"," filename})
|(START LOGICAL STANDBY APPLY [IMMEDIATE])
|(STOP LOGICAL STANDBY APPLY)
```

**[upgrade\_clauses](#upgradeclauses)::=**

```ebnf+diagram
syntax::= EXIT UPGRADE
```

**[repair\_database\_clauses](#repairdatabaseclauses)::=**

```ebnf+diagram
syntax::= CONVERT TO NORMAL
```

**[delete\_archivelog\_clauses](#deletearchivelogclauses)::=**

```ebnf+diagram
syntax::= DELETE ARCHIVELOG (ALL|UNTIL ((SEQUENCE integer [THREAD integer])|TIME date|SCN integer))[FORCE]
```

**[double\_write\_file\_clauses](#doublewritefileclauses)::=**

```ebnf+diagram
syntax::= DOUBLE_WRITE RESIZE FILE size_clauses
```

**[supplemental\_log\_clauses](#supplementallogclauses)::=**

```ebnf+diagram
syntax::= (ADD|DROP) SUPPLEMENTAL LOG (DATA ["(" (ALL|PRIMARY KEY) {"," (ALL|PRIMARY KEY)}")" COLUMNS] | TABLE TYPE "("(HEAP|TAC|LSC) {"," ( HEAP|TAC|LSC)}")")
```

**[flashback\_database\_clauses](#flashbackdatabaseclauses)::=**

```ebnf+diagram
syntax::= FLASHBACK [ON|OFF]
```

**[add\_instance\_clauses](#addinstanceclauses)::=**

```ebnf+diagram
syntax::= ADD INSTANCE [ "(" (logfiles_clause | UNDO TABLESPACE DATAFILE datafiles_clause) ")" ]
```

<span id="startupclauses" name="startupclauses" class="yaslink"></span>

### 1. startup\_clauses

This statement is used to MOUNT and OPEN the database for user access.

MOUNT is the state after the database mounts physical files, while OPEN is the state after the database is opened. For detailed database startup information, please refer to the [Instance Startup and Shutdown](../../../Database Administration/Basic Database Management/Instance Startup and Shutdown) section.



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

<span id="databasefileclauses" name="databasefileclauses" class="yaslink"></span>

### 2. database\_file\_clauses

This statement is used to control the automatic extension of data files and to specify sizes. 

This action requires the database to be in OPEN state. When setting data file auto-extend or RESIZE data file, use the tempfile option for TEMP tablespaces and SWAP tablespaces, while other tablespaces should use the datafile option.

#### 2.1. AUTOEXTEND OFF

Disables the auto-extension for a specific data file, and the NEXT_SIZE and MAX_SIZE of that data file are set to 0.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND OFF;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND OFF;
```

#### 2.2. AUTOEXTEND ON

Enables auto-extension for a specific data file, where NEXT is used to specify the size of the next space to be extended in Bytes. If not specified, the default value is 8K blocks; MAXSIZE specifies the maximum space for auto-extension, UNLIMITED means no limit, and if not specified, the default is 64M blocks.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
```

#### 2.3. RESIZE

Re-specifies the size of a specific data file.

This functionality is only applicable to Standalone Deployment.

When creating data files under tablespaces, setting the auto-extend can help meet user requirements for larger data files. However, if storage space is limited, or if a data file has been over-specified in size but utilizes very little, or if the data file contains a large amount of deleted temporary data that has not been reclaimed, it would be necessary to use resize to shrink the data file and free up disk space.

When resizing the data file to be larger, set the file to your desired size to avoid frequent resource requests.

The size after resizing must be between 128 blocks to 64MB.

A resize operation may not always succeed; for instance, trying to increase its size may result in insufficient disk space, or when decreasing its size, the current valid data size of the file may exceed the specified value.

Note: The data file in the UNDO tablespace can only be increased, not decreased.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' RESIZE 1048576;
ALTER DATABASE TEMPFILE '?/dbfiles/temp' RESIZE 1048576;
```

#### 2.4. OFFLINE [DROP]

Changes a specific data file to offline status, which can be executed when the database is in either MOUNT or OPEN state. 

> **Warn**:
> 
> After this operation is performed, the tablespace to which this data file belongs will be set to OFFLINE status, and all other data files in this tablespace will be set to RECOVER status.


Scenarios for executing offline:

* A data file is damaged or missing, preventing the database from opening. This can only be executed when the database is in MOUNT state.
* When there is data in the tablespace's data file, it cannot be deleted, but the data file is no longer needed.
* It is necessary to isolate data and temporarily take a specific data file offline.

Description of executing offline:

* This operation should ideally be performed when the database is in OPEN state, except when there is a damaged or missing data file that prevents the database from opening.
* The data file may be brought back online later and still belong to the database, hence it still requires backups. However, if the name of the data file has been changed, the backup may fail because the file cannot be found.
* If an abnormal state is detected in the database, check V$DIAG_INCIDENT to determine whether the abnormality is due to file damage. If so, the affected data file can be taken offline.
* Additional notes when performing this operation while the database is in MOUNT state:
    * In some cases (e.g. during the open operation, redo logs that created the tablespace are applied), the database may repair the offline data file and related tablespace and bring them back online.
    * Once taken offline, querying V$DATAFILE after opening the database will not correctly show the creation time of that file unless it has been brought online again.
    * Performing this operation on the standby database and then opening the database may result in the standby database being in need of repair state. It is advisable to perform this operation on the primary database to ensure that the standby database synchronizes the same files offline via redo logs.

Constraints of executing offline:

* Only one data file can be taken offline at a time.
* Data files in the built-in tablespaces cannot be taken offline.
* After a data file is taken offline, it becomes read-only and cannot be resized or have auto-extension settings altered.
* The primary database can perform this operation while in either mount or open state; however, the standby database can only perform it in mount state.

##### 2.4.1. DROP

When the database is not in archiving mode, executing data file offline must specify this option. If archiving mode is enabled, this option is ignored, meaning OFFLINE DROP = OFFLINE.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE;

-- In non-archiving mode, the following command is the only way to take the data file offline
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE DROP;
```

#### 2.5. CONVERT FILENAME [INCLUDING ARCHIVELOG]

When the entire database is migrated to a different directory, this syntax can be used to convert the paths recorded in the control files to allow the database to continue to start and operate normally. This statement is only used when the instance is in NOMOUNT stage.

The specific steps for using this statement are as follows:

1. Update the startup path: Modify the CONTROL_FILES parameter in the configuration parameter file to the current database startup path, or remove the existing configuration to use the default configuration for startup.
2. (Optional) If you need to update the storage path of archive log files: Modify the ARCHIVE_LOCAL_DEST parameter in the configuration parameter file to set the current archive path for the database, or remove the existing configuration to use the default storage path for archive log files.
3. Configure the data file path conversion parameter DB_FILE_NAME_CONVERT to convert the old file paths to the new ones. At the same time, confirm whether dual-write is needed; if dual-write is required, ensure that the data file conversion path parameters can apply to the dual-write file paths.
4. Configure the online log file path conversion parameter REDO_FILE_NAME_CONVERT to convert the old file paths to the new ones.
5. Configure the DATABUCKET path conversion parameter DB_BUCKET_NAME_CONVERT to convert the old file paths to the new ones.
6. Start the database to NOMOUNT state, and depending on whether it is necessary to convert archive log files, decide whether to explicitly specify the INCLUDING ARCHIVELOG clause.
7. Start the database to OPEN state.

##### 2.5.1. INCLUDING ARCHIVELOG

Specify this clause when you need to convert archive log files' paths.

***Example***

```sql
ALTER DATABASE CONVERT FILENAME;

-- Path conversion with the inclusion of archive log files is done with the following statement
ALTER DATABASE CONVERT FILENAME INCLUDING ARCHIVELOG;
```

<span id="logfileclauses" name="logfileclauses" class="yaslink"></span>

### 3. logfile\_clauses

This statement is used to set the archiving mode of the database's redo logs, configure the protection mode of the standby database, add, delete, etc.

#### 3.1. ARCHIVELOG/NOARCHIVELOG

Enables or stops the database's log archiving mode.

In Standalone Deployment, this operation requires the database instance to be in MOUNT state.

In YAC Deployment, this operation requires the current instance to be in MOUNT state while other instances are in NOMOUNT state.

When the database is in replication mode (primary/standby standalone deployment or primary/standby cluster deployment), it is not possible to switch from archiving mode to non-archiving mode.

***Example***

```sql
ALTER DATABASE ARCHIVELOG;
 
ALTER DATABASE NOARCHIVELOG;
```

<span id="set_standby_clause" name="set_standby_clause" class="yaslink"></span>

#### 3.2. SET STANDBY DATABASE TO

Specifies the protection mode of the standby database, with the default value being MAXIMIZE PERFORMANCE. The protection modes are as follows:

* MAXIMIZE PERFORMANCE: In this mode, the primary database transactions can be committed without waiting for the standby database to receive the logs, ensuring the availability and performance of the primary database, but data may be lost if the primary database fails.

* MAXIMIZE PROTECTION: In this mode, the data protection of the standby database takes priority over the availability of the primary database. By default, transactions on the primary database are only committed after the logs are synchronized with the standby database and written to disk (if COMMIT_WAIT = NOWAIT, then primary database transactions can commit without waiting for the logs to be written). If the synchronized standby database fails, the primary database will switch to read-only mode after some time.

* MAXIMIZE AVAILABILITY: In this mode, when the synchronized standby database is normal, by default transactions on the primary database can only be committed after the logs are written to the synchronized standby database (if COMMIT_WAIT = NOWAIT, then primary database transactions can commit without waiting for the logs to be written); when the synchronized standby database fails, transactions will not be blocked, ensuring database availability.

> **Note**: 
>
> In YAC Deployment, setting maximize protection mode requires all active instances to be in OPEN state.
>
> In YAC Deployment, the TIMEOUT field cannot be used.

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

<span id="add_logfile" name="add_logfile" class="yaslink"></span>

#### 3.3. ADD LOGFILE

Adds a new redo log to the database while allowing multiple files to be added separated by commas. This operation requires the database to be in OPEN state.

##### 3.3.1. STANDBY

Specifying the STANDBY clause adds redo log files to the standby redo log. If this field is not specified, one redo file will be added to the online redo log.

In YAC Deployment, redo log files are uniquely owned by each instance, and instances are only allowed to add redo log files for themselves.

##### 3.3.2. filename

Specifies the filename of the newly added redo log file.

##### 3.3.3. SIZE

Specifies the size of the newly added redo log file.

The minimum size of the redo log file is influenced by the parameters DB_BLOCK_SIZE, MAX_SESSIONS, and REDO_BUFFER_SIZE. The minimum value can be referenced with the formula: `DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2`.

##### 3.3.4. BLOCKSIZE

The block size of the redo log file, with a default of 4096, can be manually specified as an integer multiple of 512. The actual size of the created file will be a multiple of BLOCKSIZE; if the specified size is not a multiple, it will be rounded up to the nearest multiple.

##### 3.3.5. PARALLEL

Specifies the degree of parallelism for creating the redo log file, with a range from 1 to 8. If not specified, the system dynamically adjusts the degree of parallelism based on the file size; for example, it will be 1 if the file is not larger than 1G, 8 if over 128G, and 4 if between 1G and 128G.

***Example***

```sql
ALTER DATABASE ADD LOGFILE ('?/dbfiles/redo5' SIZE 72355840,'?/dbfiles/redo6' SIZE 72355840);
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo6' SIZE 72355840 BLOCKSIZE 512;
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo7' SIZE 72355840 PARALLEL 4;
ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/redo8' SIZE 72355840 BLOCKSIZE 512;
```

<span id="drop_logfile" name="drop_logfile" class="yaslink"></span>

#### 3.4. DROP LOGFILE

Deletes an existing redo log. It is not permitted to delete a redo log that is currently in use. This operation requires the database to be in OPEN state.

##### 3.4.1. STANDBY

Specifying the STANDBY clause deletes a standby redo log. If this field is not specified, it deletes an online redo log.

In a YAC, redo logs are uniquely owned by each instance and only allow each instance to delete its own redo logs.

***Example***

```sql
ALTER DATABASE DROP LOGFILE '?/dbfiles/redo5';
ALTER DATABASE DROP STANDBY LOGFILE '?/dbfiles/redo8';
```

#### 3.5. CLEAR LOGFILE

Reinitializes the online redo file. If the redo file does not exist or has been mistakenly deleted, this statement will recreate that file, commonly used for handling scenarios such as damaged or missing redo files.

##### 3.5.1. UNARCHIVED

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

<span id="standbydatabaseclauses" name="standbydatabaseclauses" class="yaslink"></span>

### 4. standby\_database\_clauses

This statement is used to switch between the primary and standby databases. For detailed operations of primary and standby databases, refer to the [High Availability Manual](../../../High Availability/Overview of YashanDB High Availability).

#### 4.1. CONVERT TO PHYSICAL STANDBY

Switches from the primary database to the standby database.

***Example***

```sql
ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
```

> **Note**: 
>
> - The role of the database must be PRIMARY, and the executing instance must be in MOUNT state.
> - In YAC Deployment, only instance 1 and if that instance is MASTER_ROLE can perform this operation.

#### 4.2. SWITCHOVER

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
> - In YAC Deployment, the primary database's instance 1 must be alive.
> - In YAC Deployment, switchover performance is affected by checkpoints, waiting for non-instance 1 to complete full checkpoints before performing this operation.

#### 4.3. FAILOVER

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
> - In YAC Deployment, instance 1 executes this operation to switch to the primary database, while other instances need to be opened manually.
> - RESET ID is only used in automatic failover statements issued by yasom in election scenarios; **do not manually specify this option**.

#### 4.4. RECOVER MANAGED STANDBY DATABASE CANCEL

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to stop the current apply operation. 

If a foreground apply is in process, this thread will be interrupted and exited.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
```

#### 4.5. RECOVER MANAGED STANDBY DATABASE

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to start the standby database apply; to exit, it is necessary to execute the cancel apply SQL statement.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
```

##### 4.5.1. UNTIL SCN integer

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to start the standby database apply and to exit when reaching the specified SCN. 

At this point, to exit, it is necessary to execute the cancel apply SQL statement.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123;
```

##### 4.5.2. DISCONNECT FROM SESSION

This SQL statement is executed in standby database state when it is open. The purpose of this statement is to apply in the background, allowing the current session to perform other tasks.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123 DISCONNECT FROM SESSION;
```

#### 4.6. TO LOGICAL STANDBY

This statement is used to convert a physical standby database into a logical standby database.

##### 4.6.1. KEEP IDENTITY

If you want to use the rolling upgrade functionality provided by the logical standby database and return to the original configuration of the primary database and physical standby database, please use this clause.
> **Note**: 
>
> This clause is only used in rolling upgrade scenarios and should not be used to create a general logical standby database.

##### 4.6.2. db\_name

Specifies the database name to identify the new logical standby database. 

After constructing the logical standby database in this way, it is necessary to perform the ALTER DATABASE OPEN RESETLOGS operation.

#### 4.7. REGISTER ARCHIVELOG

This statement is used to manually register archiving. The functionality constraints of this SQL include:

* After RESTORE DATABASE and when the database is not open, this SQL can be used to manually register archiving.
* After recovery or creation of completeness, the operation object must be the standby database, and the configuration parameter SANDBOX_STANDBY must be TRUE.
* The specified archive path can be an absolute path or a filename. When using a filename, the default path is the archive path (configuration parameter ARCHIVE_LOCAL_DEST).

***Example*** for Standalone Deployment and YAC Deployment

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

#### 4.8. OR REPLACE

If an archive is found to be already registered, replace the original registered archive. This operation is risky and should be used with caution.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC', '/home/yashan/archive/arch_0_2.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';
```

#### 4.9. START LOGICAL STANDBY APPLY

This statement is used to enable the logical apply functionality for the logical standby database, which is off by default.

#### 4.10. STOP LOGICAL STANDBY APPLY

This statement is used to orderly stop the log apply service of the logical standby database.

It is not applicable to physical standby databases.

<span id="upgradeclauses" name="upgradeclauses" class="yaslink"></span>

### 5. upgrade\_clauses

This statement is used for database version upgrades.

#### 5.1. EXIT UPGRADE

After the database upgrade is complete, you can directly exit the upgrade mode into normal OPEN mode without restarting.

***Example***

```sql
ALTER DATABASE EXIT UPGRADE;
```

<span id="repairdatabaseclauses" name="repairdatabaseclauses" class="yaslink"></span>

### 6. repair\_database\_clauses

This statement is used when the database is in an ABNORMAL state and requires DBA intervention for repair.

#### 6.1. CONVERT TO NORMAL

When the database encounters a failure and is set to read-only, the database is in a fault state. After DBA repairs, this statement can be used to manually switch the database to normal mode.

> **Note**: 
>
> This statement cannot be used to set the database status to normal when the database is in an abnormal state due to resource errors.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE CONVERT TO NORMAL;
```

<span id="deletearchivelogclauses" name="deletearchivelogclauses" class="yaslink"></span>

### 7. delete\_archivelog\_clauses

This statement is used to manually clean up the archiving files of the database to free up disk space.

The conditions for manually cleaning up archive log files are determined by the ARCHIVELOG_DELETION_POLICY or ARCH_CLEAN_IGNORE_MODE parameters. For specific descriptions of the cleanup conditions, refer to the [Archive Management](../../../Database Administration/Basic Database Management/Archive Management) section.

#### 7.1. DELETE ARCHIVELOG 

##### 7.1.1. ALL

Cleans up all archives that meet the cleanup conditions.

##### 7.1.2. UNTIL SEQUENCE integer [THREAD integer]

Cleans up archives that meet cleanup conditions prior to the specified sequence number. If no instance is specified, the default will remove archives for instance 1.

##### 7.1.3. UNTIL TIME date

Cleans up archives that were generated prior to the specified time and meet cleanup conditions.

##### 7.1.4. UNTIL SCN integer

Cleans up archives that were generated prior to the specified SCN and meet cleanup conditions. (Comparing with NEXT_CHANGE# in V$ARCHIVED_LOG).

###### 7.1.4.1. FORCE 

Forces the cleanup of archives without considering cleanup conditions.

***Example***

```sql
ALTER DATABASE DELETE ARCHIVELOG ALL;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME TO_DATE('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss');

-- Forceful archive cleanup
ALTER DATABASE DELETE ARCHIVELOG ALL FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5 FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME TO_DATE('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss') FORCE;
```

<span id="doublewritefileclauses" name="doublewritefileclauses" class="yaslink"></span>

### 8. double\_write\_file\_clauses

This statement is used to re-specify the size of the double write file.

In YAC Deployment, there is no double write file.

In ISC Distributed Cluster Deployment, it is not allowed to adjust the size of the double write file.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DOUBLE_WRITE RESIZE FILE 32M;
```

<span id="supplementallogclauses" name="supplementallogclauses" class="yaslink"></span>

### 9. supplemental\_log\_clauses

This statement is used for configuring database-level supplemental logging, which is not applicable to ISC Distributed Cluster Deployment.

When supplemental logging is enabled, the database will additionally record some data in redo, including the original SQL text of DDL, and index information used for locating rows during update and delete, etc.

With supplemental logging, corresponding DDL and DML statements can be restored through redo parsing, typically used for heterogeneous database synchronization.

Database-level supplemental logging takes effect on all selected types of user tables, and the effective state can be viewed through the dynamic view [V$DATABASE](../../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE). If the target table has [encryption](CREATE TABLE.html#columnencryptionclause) enabled, the database-level supplemental logging will still display the effective status based on the selected table types, but relevant supplemental information will not be recorded in redo.

For table-level supplemental logging, please refer to [ALTER TABLE](ALTER TABLE.html#addsupplementalloggingclause).

If you wish to use the [Ystream service](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM), you must first enable supplemental logging (either at the library or table level) before creating the Ystream service; otherwise, it will result in an error.

#### 9.1. SUPPLEMENTAL LOG DATA

SUPPLEMENTAL LOG DATA indicates minimal supplemental logging. In this mode, the redo will additionally record the DDL text and DML rowid, with minimal performance impact.

When enabling supplemental logging in ALL or PRIMARY KEY mode, minimal supplemental logging is implicitly enabled. Additionally, it cannot be turned off before disabling other modes of supplemental logging.

The priority of ALL mode is higher than PRIMARY KEY mode; when either database-level or table-level supplemental logging mode is set to ALL, that table will use ALL mode.

Database-level supplemental logging records DDLs for objects such as FUNCTION, PACKAGE, PROCEDURE, SEQUENCE, TRIGGER, SYNONYM, LIBRARY, TABLE, INDEX, TYPE, VIEW, and MATERIALIZED VIEW, but will not record DDLs that do not modify metadata.

##### 9.1.1. ALL

In ALL mode, the redo will additionally record DDL text and DML rowid, and will also record all columns in the original row during updates and deletes (except LOBs and VARCHARs and CHARs exceeding 32K).

This mode is suited for tables without primary keys, as it adds more data in redo, which has a higher performance impact.

##### 9.1.2. PRIMARY KEY

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

#### 9.2. SUPPLEMENTAL LOG TABLE TYPE

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

<span id="flashbackdatabaseclauses" name="flashbackdatabaseclauses" class="yaslink"></span>

### 10. flashback\_database\_clauses

#### 10.1. Full Flashback of Primary Database

This statement is used to configure the full flashback functionality for the database. Once enabled, the database allows flashing back to a specified point in time without backup sets.

The full flashback functionality must adhere to the following rules:

- Applicable only to Standalone Deployment.
- The database must have archiving mode enabled.
- The executing instance must be in MOUNT or OPEN state.
- If there are [permanent restore points](CREATE RESTORE POINT), the full flashback functionality cannot be disabled until all permanent restore points have been manually [deleted](DROP RESTORE POINT).
- When disabling the full flashback functionality, all common (non-permanent) restore points will be deleted.

> **Note**: 
>
> Full flashback can create a performance impact on the database (estimated at about 8% reduction), so configure accordingly based on actual usage scenarios.

#### 10.2. Full Flashback of Standby Database

The standby database flashback currently supports the following specific scenarios:

The standby database needs to failover, perform a separate business for a period, and then flash back to the time before the failover, while re-establishing a connection with the primary database.

> **Note**: 
>
> - The standby database can only enable flashback while in MOUNT state.
>
> - The standby database must create a [permanent restore point](CREATE RESTORE POINT) while enabling flashback.
>
> - Before executing failover, all archive_dest_n and replication_addr for the standby database must be set to empty after enabling flashback.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE FLASHBACK ON;
```

<span id="addinstanceclauses" name="addinstanceclauses" class="yaslink"></span>

### 11. add\_instance\_clauses

This statement is used to add a new database instance to a YAC. The syntax and rules of logfiles_clause, datafiles_clause, and others are the same as those in the corresponding clauses of [CREATE DATABASE](CREATE DATABASE).

> **Caution**:
>
> For [YAC instance expansion](../../../Installation and Upgrade/Scalability/Scalability of YAC Deployment/Scaling of Cluster Instances), it is recommended to use the *yasboot* tool for a one-click command. **It is not recommended** to execute this statement separately to add database instances.

The execution of this statement must follow the rules below:

- It is applicable only to YAC Deployment.
- It can only be executed on the primary instance (i.e., the instance where the INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE), and the primary instance must be in OPEN state. If in HA environment, this operation can only be performed on the primary instance of the main cluster.
- This statement cannot be executed concurrently with ALTER DATABASE's add_instance_clauses, switchover, data file add/delete/resize, redo file add/delete, ystream creation/running, distributed transactions, backup recovery, and other operations. It must wait for any such event to complete before proceeding to the next operation.
- During the execution of this statement, no other instances are allowed to change from nomount to mount/open state.
- A single execution of this statement can only add one instance; if needed, the statement can be executed repeatedly.
- After adding an instance, the total number of instances must not exceed the current cluster's [MAXINSTANCES](CREATE DATABASE.html#maxinstances) value.
