Executing recovery operations via SQL statement is applicable to databases in Standalone/YAC/Distributed Cluster Deployment, and the database version executing the recovery must be identical to the version that generated the backup set.

When a large amount of data needs to be restored or when there is a desire to improve recovery efficiency, the concurrency level can be appropriately increased, with a default concurrency level of 2.

During the recovery process, detailed logs of the recovery can be viewed via the log file located at $YASDB_DATA/log/run/run.log.

> **Caution**:
> 
> In a dual-copy primary/standby deployment environment, instances after executing database recovery operations will all become primary databases. If the target instance is a standby database and the original primary database is live, recovery operations cannot be performed on that instance; otherwise, it will lead to a dual-primary situation causing deployment status or data anomalies. If it is necessary to rebuild the standby database, please execute the standby database BUILD operation.

## Full Recovery

Full recovery refers to the process of decompressing and decrypting backup files from the full database backup set and restoring them to the database directory, then applying the archive log files from the backup set to restore the database to a consistent state, allowing the database to be fully restored to the backup time point.

- Restore based on a full backup set: Restore all files directly from the backup set, including control files, data files, and archive log files.

- Restore based on an incremental backup set: First restore the data files from the LEVEL 0 backup set, then overlay the pages of the database with the pages from the subsequent LEVEL 1 backup set, and finally apply the current incremental backup's archive log files to bring the database to a consistent state.

### Operational Instructions

- The RESTORE DATABASE statement can only be executed by the SYS user. For detailed syntax, please refer to [RESTORE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/RESTORE DATABASE).

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (instances with INSTANCE_ROLE field as MASTER_ROLE in the V$INSTANCE view).

- If the database backup set is encrypted, the decryption keyword and its password must be specified during recovery.

- Database environment preparation before recovery:

  - The database instance must be in the NOMOUNT phase.

  - In standalone one-primary/one-standby deployment, if [yasom election] is enabled (refer to ..\..\..\Tools Guide\yasboot\Introduction to yasboot Command\yasboot election), it must be disabled before proceeding with the recovery operation. After recovery is complete, restore the corresponding configuration as necessary.

  - In standalone one-primary/multi-standby deployment, before executing recovery operations on the primary database (especially in scenarios where only an isolated primary database remains), the automatic fallback functionality must be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to avoid automatic fallback triggered by failure to timely detect standby database heartbeats. Restore the corresponding configuration as necessary after recovery completion.
  
  - If recovering to paths for database files or log files that differ from the backup set (e.g., primary-standby replication in a high availability deployment), the DB_FILE_NAME_CONVERT and REDO_FILE_NAME_CONVERT parameters must be configured to specify file path conversion.
  
  - Manually clean up existing data files: In Standalone Deployment, there should be no data files in $YASDB_DATA/dbfiles; in YAC/Distributed Cluster Deployment, there should be no data files in the +DG0 directory (the default name for the first disk group during installation; modify it appropriately if necessary), otherwise, the YAS-00311 error will be triggered.

  - When performing a full recovery and only needing to restore the contents of the backup set, any existing archive log files in the current database (default path $YASDB_DATA/archive) must be cleared to prevent inconsistencies between the restored data and the backup set due to leftover data in the applied archive log files during the RECOVER operation.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before recovery operations.

### Restore Based on Full Backup Set

Full recovery refers to restoring the database to the backup time point based on the backup set generated from a full backup. This is a complete recovery, and once successful, the current database can be opened directly. For YAC/Distributed Cluster, after the primary instance has successfully recovered, the other instances must be started in sequence (refer to [YAC/Distributed Cluster Startup and Shutdown](../../../Database Administration/Cluster Management/Cluster Startup and Shutdown)).

***Example*** for Standalone Deployment

```sql
-- 1. Execute RESTORE DATABASE in the NOMOUNT phase. Upon completion, the database will start up to the MOUNT phase.
-- Scenario 1: Specify a concurrency level of 6 for the recovery.
RESTORE DATABASE FROM '/data/backup/full_20211209191000' PARALLELISM 6;

-- Scenario 2: For recovery based on an encrypted backup set, the DECRYPTION keyword must be specified, and ensure the password matches the original encryption.
RESTORE DATABASE DECRYPTION yas2022 FROM '/data/backup/bak_20211209191100';
 
-- 2. Execute RECOVER DATABASE.
-- If the archive log files and online logs are continuous and complete after successfully restoring the backup set, YashanDB will automatically apply all logs.
RECOVER DATABASE;

-- 3. Open the database.
ALTER DATABASE OPEN;
```

### Continuous Recovery Based on Incremental Backup Set

Continuous recovery based on incremental backup set refers to using incremental backup sets to restore the database, allowing recovery to any time point of a backup operation within the backup set chain.

For incremental backup set recovery, the LEVEL 0 backup set must be restored first, then each subsequent incremental backup set must be specified in order, ensuring the backup files of the dependent backup sets exist and are complete:

- If the zero-level incremental backup is tagged as incr_0, the ordinary level 1 incremental backups are tagged as incr_1_1, incr_1_2, ... (backup initiated in order):

   - When restoring the level 1 incremental backup tagged incr_1_2, the backup files of both incr_0 and incr_1_1 must exist and be intact; otherwise, the YAS-00313 error will be triggered.

- If the zero-level incremental backup is tagged as incr_0, the cumulative level 1 incremental backups are tagged as incr_1_1, incr_1_2, ... (backup initiated in order):

   - When restoring the level 1 incremental backup tagged incr_1_2, the backup files of the level 0 incremental backup tagged incr_0 must exist and be intact; otherwise, the YAS-00313 error will be triggered.

- If the zero-level incremental backup is tagged as incr_0, cumulative level 1 incremental backups tagged incr_1_1, ordinary level 1 incremental backups tagged incr_1_2, cumulative level 1 incremental backups tagged incr_1_3, ordinary level 1 incremental backups tagged incr_1_4, ... (backup initiated in order):

   - When restoring the level 1 incremental backup tagged incr_1_4, the backup files of the incremental backups tagged incr_0 and incr_1_3 must exist and be intact; otherwise, the YAS-00313 error will be triggered.

   - When restoring the level 1 incremental backup tagged incr_1_3, the backup files of the zero-level incremental backup tagged incr_0 must exist and be intact; otherwise, the YAS-00313 error will be triggered.

If continuous incremental backup sets include slice files of LSC tables, continuous restore must strictly follow the order of backup creation, or it may lead to slice file loss. If a restore fails, repeating the restore using the same backup set may fail again due to existing slice files. In such cases, the corresponding slice files must be manually cleared before continuing with the restore.

Based on tags, there are two optional keywords for incremental backup set recovery:

- INCREMENTAL NOREDO: When this keyword is specified, the redo and archive log files from the backup set will not be restored. After the recovery operation is complete, the database will still be unopenable for normal use, and further recovery is required from other incremental backup sets in the same incremental backup set chain. The last recovery operation in the entire chain cannot specify INCREMENTAL NOREDO.

- INCREMENTAL: Specifying this keyword indicates a complete recovery based on the incremental backup set. After the recovery operation is completed, the database will be started up to the MOUNT phase for execution of RECOVER, after which it can be started up to OPEN.

***Example*** for Standalone Deployment

```sql
-- 1. Execute RESTORE DATABASE in the NOMOUNT phase.
-- For the first recovery based on an incremental backup set, the LEVEL 0 backup set must be restored first. Specify the INCREMENTAL NOREDO parameter to speed up the recovery.
RESTORE DATABASE INCREMENTAL NOREDO FROM '/data/backup/incr_0_20211209193516';

-- 2. Based on the successful recovery at this baseline, further continuous recovery of the same database's incremental backup sets can be done; however, the last recovery must not specify INCREMENTAL NOREDO.
RESTORE DATABASE INCREMENTAL FROM '/data/backup/incr_1_20211209193740';

-- 3. Start the database to the MOUNT phase and execute RECOVER DATABASE.
ALTER DATABASE MOUNT;
RECOVER DATABASE;
-- After recovery, incremental backup set recovery cannot be performed again.

-- 4. Start the database to the OPEN phase.
ALTER DATABASE OPEN;
```

## Archive Recovery

Archive recovery refers to restoring archive log files from the archive backup set into the database's archive directory and registering them in the database after a complete restore (RESTORE DATABASE) has been performed but before executing log recovery (RECOVER DATABASE), aiming to supplement any missing archive log files in the database backup set.

For detailed syntax, please refer to [RESTORE ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/RESTORE ARCHIVELOG).

### Operational Instructions

- In YAC/Distributed Cluster Deployment, archive recovery operations can only be performed on the primary instance (instances with INSTANCE_ROLE field as MASTER_ROLE in the V$INSTANCE view).

- Archive log files can only be restored as the entire set or subset of existing archive backup sets, and the method of specifying recovery range (SCN, SEQUENCE, or time) is not constrained by the backup range specification method.

- If multiple archive backup sets need to be restored, the FROM SEARCHDIR option can be used to specify the folder of the set (the parent directory of the backup set directory).

- If the archive backup set is encrypted, the DECRYPTION keyword and decryption password must be specified during recovery. If the passwords of multiple backup sets differ, backup sets with differing passwords will be skipped when restoring from the FROM SEARCHDIR option; restoration can be executed in batches according to the keys.

- Database environment preparation before recovery:

  - The database must have completed RESTORE DATABASE and started up to the MOUNT phase but has not yet executed RECOVER DATABASE.

  - Ensure the target path for restoring archive log files exists; by omitting TO destpath, the files will be restored to the path configured in the ARCHIVE_LOCAL_DEST parameter (default value is $YASDB_DATA/archive).

### Full Restore

Full restore refers to restoring all archive log files from the existing archive log files backup set. Specifying the ALL keyword indicates that a full restore is to be performed.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- At this point, the database has completed the RESTORE DATABASE operation and started up to the MOUNT phase but has not yet completed RECOVER DATABASE.
RESTORE ARCHIVELOG ALL FROM BACKUPSET '/data/backup/all_20211209191000';
-- After RESTORE ARCHIVELOG, proceed with RECOVER DATABASE as needed.
```

### SCN-Based Recovery

SCN-based recovery refers to specifying the range of archive log files to be recovered using SCN. The specific target range can be specified using the following keywords:

- FROM SCN n: Indicates that the recovery range includes the log file containing SCN n to the endpoint file of the current backup set.

- SCN BETWEEN n AND m: Indicates that the recovery range includes the archive log files containing SCN in the range of [n, m].

- UNTIL SCN n: Indicates that the recovery range includes the archive log files from the start of the current backup set to the log file containing SCN n.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- At this point, the database has completed the RESTORE DATABASE operation and started up to the MOUNT phase but has not yet completed RECOVER DATABASE.
RESTORE ARCHIVELOG FROM SCN 505411989407080448 FROM BACKUPSET '/data/backup/scn_20211209191000';
-- After RESTORE ARCHIVELOG, proceed with RECOVER DATABASE as needed.
```

### SEQUENCE-Based Recovery

SEQUENCE-based recovery refers to specifying the range of archive log files to be recovered using the sequence number of the archive log files. The specified files in the statement must exist. The specific target range can be specified using the following keywords:

- FROM SEQUENCE x: Indicates that the recovery range includes the log files from the sequence number x to the endpoint file of the current backup set.

- SEQUENCE BETWEEN x AND y: Indicates that the recovery range includes all archive log files in the interval [x, y].

- UNTIL SEQUENCE x: Indicates that the recovery range includes the archive log files from the start of the current backup set to the sequence number x.

You can obtain the current existing archive log files' sequence numbers (SEQUENCE# field) and other details through the V$ARCHIVED_LOG view.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- At this point, the database has completed the RESTORE DATABASE operation and started up to the MOUNT phase but has not yet completed RECOVER DATABASE.
RESTORE ARCHIVELOG UNTIL SEQUENCE 10 FROM BACKUPSET '/data/backup/seq_20211209191000';
-- After RESTORE ARCHIVELOG, proceed with RECOVER DATABASE as needed.
```

### TIME-Based Recovery

TIME-based recovery refers to specifying the range of archive log files to be recovered using time, actually restoring the entire archive log files corresponding to a specific log at the specified time point.

Time formats can include:

- `'2021-12-09 19:35:55'`: This format must match the DATE_FORMAT parameter specified in the current session.

- `TO_DATE('2021-12-09 19:35:55','yyyy-mm-dd hh24:mi:ss')`: Converted using the TO_DATE function.

- `TO_TIMESTAMP('2021-12-09 19:35:55','yyyy-mm-dd hh24:mi:ss')`: Converted using the TO_TIMESTAMP function.

The specific target range can be specified using the following keywords:

- FROM TIME t: Indicates that the recovery range includes the log files starting from the first log at time point t to the newest archive log files in the current backup set.

- TIME BETWEEN t1 AND t2: Indicates that the recovery range includes all log records in the interval [t1, t2].

- UNTIL TIME t: Indicates that the recovery range includes the log files from the earliest archive log files in the current backup set to the last log file at time t.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- At this point, the database has completed the RESTORE DATABASE operation and started up to the MOUNT phase but has not yet completed RECOVER DATABASE.
RESTORE ARCHIVELOG UNTIL TIME TO_DATE('2024-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') FROM BACKUPSET '/data/backup/time_20211209191000';
-- After RESTORE ARCHIVELOG, proceed with RECOVER DATABASE as needed.
```

## Point-In-Time Recovery (PITR)

Point-in-Time Recovery (PITR) refers to restoring the database to the state at a specific time between the backup time point and the latest time point based on the database backup set's database files and archive log files.

### Operational Instructions

- The RESTORE DATABASE statement can only be executed by the SYS user.

- In YAC/Distributed Cluster Deployment, recovery operations can only be performed on the primary instance (instances with INSTANCE_ROLE field as MASTER_ROLE in the V$INSTANCE view).

- If slice files of the LSC table have been generated after the backup set, this may lead to data inconsistency in the LSC table after recovery in the following scenarios:

  - All original slice files exist, but an old time point is used for recovery, resulting in additional slice files in the LSC table.

  - The original slice files are cleaned up, and using PITR may lead to loss of data stored within the slice files in the LSC table.

- Database environment preparation before recovery:

  - The database instance must be in the NOMOUNT phase.

  - In standalone one-primary/one-standby deployment, if [yasom election] is enabled, it must be disabled before proceeding with recovery operations. After recovery is complete, restore the corresponding configuration as necessary.

  - In high availability deployment, if automatic fallback functionality is enabled (HA_ELECTION_LEADER_LEASE_ENABLED set to TRUE on the primary node), this functionality must be disabled before recovery operations, with any necessary configuration restored afterwards.

  - If recovering to different database file or log file paths from the backup set (e.g., primary-standby replication in high availability deployment), the DB_FILE_NAME_CONVERT and REDO_FILE_NAME_CONVERT parameters must be configured to specify file path conversion.
  
  - Manually clean up existing data files: In Standalone Deployment, there should be no data files in $YASDB_DATA/dbfiles; in YAC/Distributed Cluster Deployment, there should be no data files in the +DG0 directory (the default name for the first disk group during installation; modify it accordingly).

  - Before executing PITR, ensure that the archive log files (default path $YASDB_DATA/archive) exist, such as by restoring archive log files via RESTORE ARCHIVELOG.

  - If the backup set contains encrypted objects, open the corresponding key wallet file before recovery operations.

### Steps

1. Execute the [RESTORE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/RESTORE DATABASE) statement to restore database files based on the specified database backup set.

2. Execute the [RESTORE ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/RESTORE ARCHIVELOG) statement to restore and register the archive log files based on the specified archive log files backup set.

3. Execute [RECOVER DATABASE UNTIL](../../../Development Guide/SQL Reference Manual/SQL Statements/RECOVER DATABASE) statement to recover the database to a specific state between the backup point and the latest time point (via time or SCN).

4. Reset the redo timeline.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- 1. Restore database files based on the database backup set.
RESTORE DATABASE FROM '/data/backup/full_20211209191000';

-- 2. Restore archive log files based on the archive log files backup set.
RESTORE ARCHIVELOG ALL FROM BACKUPSET '/data/backup/all_20211209191000';

-- 3. Recover the database to the specified time point.
-- Scheme 1: Specify the log sequence number for recovery at a specific time point.
RECOVER DATABASE UNTIL TIME TO_DATE('2021-12-09 19:35:55','yyyy-mm-dd hh24:mi:ss'); 

-- Scheme 2: Specify SCN (obtained via the V$DATABASE view) for time point recovery, assuming 258477020237086720 is the SCN value recorded by the user before database failure.
SELECT CURRENT_SCN FROM V$DATABASE;
RECOVER DATABASE UNTIL SCN 258477020237086720;
 
-- Reset the redo timeline and open the database.
ALTER DATABASE OPEN RESETLOGS;
```
