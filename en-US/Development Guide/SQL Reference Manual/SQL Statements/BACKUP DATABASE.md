General Description
----

BACKUP DATABASE is used to perform a backup of the current database, which creates a copy of the database files in the form of a backup set for persistence.

Backups can be classified into full backups and incremental backups:

*   [Full Backup](#full): A complete copy of all data at a certain point in time, which does not rely on previous backup sets. A full backup set can restore all data.

*   [Incremental Backup](#incremental): The first execution is a baseline backup (level 0), followed by subsequent backups that only need to back up incremental data (level 1), improving efficiency and saving disk space. Restoration requires starting from the baseline backup set and sequentially restoring all incremental backups.

The database can only be backed up when its status is OPEN, and the archiving mode must be enabled (adjust archiving mode using the [ALTER DATABASE](ALTER DATABASE) statement).

In the following cases, this statement can only be used for backup and recovery operations via YashanDB's companion tool [*yasrman*](../../../Tools Guide/yasrman/00yasrman):

- When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), this statement cannot be used. To perform backup and recovery, you must use the *yasrman*.

- In ISC Distributed Cluster Deployment, executing this statement can only back up the operation node — it cannot back up the entire distributed cluster. To back up the distributed cluster, you must use the *yasrman*.

You can query the backup progress through the v$backup_progress view, and the generated backup set information can be viewed through the dba_backup_set view.

For detailed operation descriptions on backup recovery, please refer to [Backup Recovery](../../../Database Administration/Backup and Recovery/00Backup and Recovery).

> **Note**: 
>
> To back up encrypted objects, the key wallet file must first be backed up manually. The corresponding key wallet file must be opened before performing the recovery operation.

Statement Definition
----

**backup database::=**

```ebnf
= BACKUP DATABASE ((([FULL] [FORCE] |(INCREMENTAL LEVEL integer [CUMULATIVE|INDEPEND|BASE ON tag_name])) [backupCommonSpecifier])
|(DELETE BACKUPSET [IF EXISTS] (TAG tag_name|PATH backup_path)) | CANCEL).
```

**[backupCommonSpecifier](#backupCommonSpecifier)::=**

```ebnf
= (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
TAG tag_name|
FORMAT backup_path|
SECTION SIZE size_clause)
{" " (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
TAG tag_name|
FORMAT backup_path|
SECTION SIZE size_clause)}.
```

<span id="full" name="full"></span>

### FULL

This statement is used to specify the backup method as a full database backup; if omitted, the default is full backup.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Perform full backup
BACKUP DATABASE FULL;
 
-- View backup information through the view
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE    INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                       TRUNC_LSN
-------- ------- --------------- ------------- ---------------------------------------------------------------- ----------------------- -----------
       1 FULL                  0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275         187034
```

### FORCE

 

Specifies whether to force a full backup on a read-only database, such as the standby database in a primary-standby deployment or a database in an abnormal state.

Forced backup does not record system tables. When backing up a standby database, the standby database does not need to be connected to the primary database.

If a backup is executed when the database is in an abnormal state, the FORCE keyword must be specified.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE FULL FORMAT 'backup1' TAG 'backup1' PARALLELISM 8 SECTION SIZE 128M FORCE;
```

<span id="incremental" name="incremental"></span>

### INCREMENTAL

This statement is used to specify the backup method as incremental backup.

**LEVEL integer**

 

Used to specify the incremental backup level, it cannot be omitted, and the value can be LEVEL 0 or LEVEL 1.

- LEVEL 0 indicates the first incremental backup (backup scope is equivalent to full backup, but a physical identifier distinguishing it from the full backup is added in the backup summary file).

- LEVEL 1 indicates a non-first incremental backup.



When performing incremental backup, INDEPEND|BASE ON tag_name or CUMULATIVE can also be specified as needed.

#### INDEPEND|BASE ON tag_name

This statement is used to specify a backup set that generates an independent incremental backup chain. Such backup sets show FALSE in the DEFAULT_BASE field of the DBA_BACKUP_SET view.

- The INDEPEND field can only be used when the incremental backup is specified as LEVEL 0.

- The BASE ON field can only be used when the incremental backup is specified as LEVEL 1, and the tag_name specified backup set must be one that belongs to an independent chain (i.e., shown as FALSE in the DEFAULT_BASE field of the DBA_BACKUP_SET view).

Applicable only for Standalone Deployment.

#### CUMULATIVE

 

For LEVEL 1 incremental backups, explicitly specifying CUMULATIVE indicates that the incremental backup method is cumulative; otherwise, it is a normal incremental backup.

*   Normal Incremental Backup: The baseline (LSN) of a normal incremental backup is the previous incremental backup (either level 0 or level 1). To recover from this type of incremental backup set, at least one other incremental backup set is also needed.

*   Cumulative Incremental Backup: The baseline (LSN) of a cumulative incremental backup is the most recent level 0 incremental backup. To recover from this type of incremental backup set, only one other incremental backup set is required.



> **Note**: 
>
> When the backup specifies CUMULATIVE, the attributes INDEPEND or BASE ON cannot be specified simultaneously.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Perform first incremental backup (LEVEL 0)
BACKUP DATABASE INCREMENTAL LEVEL 0;

-- View backup information through the view
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
									 
-- Perform cumulative incremental backup
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE;

-- View backup information through the view
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
       3 INCREMENTAL                 1             1 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320030305       bak_2023121320030305       187042
```

<span id="backupCommonSpecifier" name="backupCommonSpecifier"></span>

### backupCommonSpecifier

Used to specify common configurations for the backup operation such as compression, encryption, and parallelism.

#### COMPRESSION



Specifies the compression attributes of the backup set, including the compression algorithm and compression level. If only the `COMPRESSION` keyword is specified (algorithm and level are omitted), the default compression algorithm is ZSTD and the default compression level is LOW.

Each backup set in incremental backups can use different compression algorithms and levels, which does not affect recovery.

**Compression Algorithm**

The compression algorithm is specified via the `ALGORITHM` keyword, and the following algorithms are supported:

- ZSTD: Default algorithm, with relatively higher compression ratio.

- LZ4: Relatively higher compression speed.

**Compression Level**

Compression levels include:

- LOW (indicating the lowest compression ratio, with the highest compression speed)

- MEDIUM

- HIGH



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION;
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION ALGORITHM lz4 HIGH;
```

#### ENCRYPTION



Specifies the encryption attributes of the backup set, including the encryption algorithm and key. If the encryption algorithm is not explicitly specified, the default algorithm is SM4.

Each backup set in incremental backups must either all be encrypted or all not be encrypted, and the keys of each backup set must be consistent.

**Encryption Algorithm**

The following encryption algorithms are supported:

- AES128

- AES192
- AES256
- SM4


**Key**

The encryption key for encrypted backups is specified via the IDENTIFIED BY keyword, and its constraints follow the same rules as the [database user password rules](./CREATE USER.md#password).



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- 1. Perform first incremental backup and encrypt
BACKUP DATABASE INCREMENTAL LEVEL 0 ENCRYPTION IDENTIFIED BY 12345;

-- Subsequent incremental backups must be encrypted
BACKUP DATABASE INCREMENTAL LEVEL 1;
YAS-00607 encryption algorithm error, algorithm name 'UNKNOWN', the baseline backup set is encrypted

-- Subsequent incremental backups must be encrypted and use the same key
BACKUP DATABASE INCREMENTAL LEVEL 1 ENCRYPTION SM4 IDENTIFIED BY 123;
YAS-04261 invalid password: the backup password entered is inconsistent with the baseline backup password

-- The encryption algorithm for subsequent incremental backups can be different
BACKUP DATABASE INCREMENTAL LEVEL 1 ENCRYPTION SM4 IDENTIFIED BY 12345;
Succeed.
```

#### PARALLELISM



Specifies the parallel degree for multithreaded backup, with a range of [1,16]; if omitted, the default is 2.



#### TAG



Specifies the alias of the backup set, with a maximum length of 64 (including the terminating character '\\0').



#### FORMAT



Specifies the name of the backup set file (can include a path). If omitted, the default is `$YASDB_DATA/backup/bak_{date}`. If specified without a path, the file is created by default in the `$YASDB_DATA/backup` directory.

In YAC/Distributed Cluster Deployment, you can specify a [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) path to store the backup set on shared storage; if a local path is specified, the backup set will be saved on the server where the instance resides.

#### SECTION SIZE



This statement is used to specify the file shard size; files exceeding this size will be split into multiple smaller files for backup. The range is [128M,32T], and if omitted, the default is the system-calculated optimal value.

Configuration recommendations are as follows:

- It is recommended to specify the shard specification based on the actual data file size.

  Assuming the maximum size of a single data file in YashanDB is MAXSIZE, it is recommended that the shard specification be less than MAXSIZE/(PARALLELISM*2).

- It is recommended that the shard specification be less than 4G.

If the specified value is not an integer multiple of 1M, it will be rounded down to the nearest 1M alignment.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE FULL FORMAT 'backup' TAG 'backup' PARALLELISM 8 SECTION SIZE 128M;
```

### DELETE BACKUPSET

This statement is used to delete backup sets as well as the backup set records in DBA_BACKUP_SET.

#### IF EXISTS

An optional parameter for the backup set deletion command. If unsure about the TAG of the specified backup set or whether the physical file of the backup set exists, this option can be specified to return a successful deletion regardless.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE DELETE BACKUPSET TAG 'backup';
BACKUP DATABASE DELETE BACKUPSET PATH '/data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275';
BACKUP DATABASE DELETE BACKUPSET IF EXISTS TAG 'backup';
```

> **Note**: 
>
> Executing the deletion command for a specified backup set TAG will delete both the backup view content and the physical backup set. Specifying the physical backup set directory will only delete the physical backup set; the corresponding view content remains.
>
> If backup view remnants exist and the physical backup files have been deleted, specify the IF EXISTS option to execute the backup set deletion command, which will succeed and return a successful deletion result.

#### TAG

Used to specify the backup set to be deleted, confirming the target of the backup set by its alias. TAG information can be queried from the DBA_BACKUP_SET view.

#### PATH

Used to specify the backup set to be deleted, confirming the target of the backup set by its backup file path. PATH information can be queried from the DBA_BACKUP_SET view.

### CANCEL

This statement is used to cancel the current backup, restore or build task.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE CANCEL;
```
