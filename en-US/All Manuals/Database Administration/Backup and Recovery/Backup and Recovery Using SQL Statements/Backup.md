Backing up operations through SQL statements are applicable for Standalone/YAC/Distributed Cluster Deployment databases. Depending on the backup object, it can be divided into:

- Backup Database: Execute the [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE) statement to back up the entire database. The generated database backup set information can be queried through the DBA_BACKUP_SET view.

- Backup Archive Log Files: Execute the [BACKUP ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP ARCHIVELOG) statement to back up the database's archive log files. The generated archive log files backup set information can be queried through the DBA_ARCHIVE_BACKUPSET view.

Backup operations consume a certain amount of system and database resources. Please choose the backup window according to the business load. When the amount of data for the objects to be backed up is large or when backup efficiency needs to be improved, the concurrency can be appropriately increased. The default level of concurrency is 2.

During the backup process, the V$BACKUP_PROGRESS view can be used to view the real-time backup progress, and the $YASDB_DATA/log/run/run.log runtime log can be consulted for detailed logs of the backup process.

## Backup Database

### Operation Instructions

- The BACKUP DATABASE statement can only be executed by the SYS superuser or users with SYSDBA or SYSBACKUP privilege.

- Backing up the database does not allow concurrent operations for adding, deleting, or resizing data files such as tablespaces and redo files. When executing backup operations on a standby database, redo related to apply operations of the standby database will be blocked, leading to an increase in log gaps between primary and standby. The standby database will only apply primary database redo after the backup operation is completed.

- If performing a full backup in read-only mode or in an abnormal state, the FORCE keyword can be specified, but forced backups will not be recorded in system tables.

- In maximize protection mode for primary/standby, if the standby database disconnects, transactions on the primary database cannot be committed, and continuing to execute backups on the primary database may occasionally cause the backup process to hang. Switching the primary database transaction to maximize availability mode will allow the backup to continue.

- In YAC/Distributed Cluster Deployment, only one instance can perform backup operations at any one time, and if any instance goes down during the backup, all backup operations of that cluster will be interrupted.

- Database environment preparation before backup:

    - The database instance must be in OPEN state and archiving mode must be enabled.

    - If you need to customize the path to save the backup files, ensure that the path is empty and has sufficient disk space. The FORMAT keyword can be used to customize the file name of the backup set (including the storage path). If omitted, the default path will be $YASDB_DATA/backup/bak_{date}. In YAC/Distributed Cluster Deployment, it can also be specified as a YFS path to store the backup files in shared storage.

    - If backing up encrypted objects, the key wallet file must be backed up manually first.

### Backup Methods

#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates a full backup.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE FULL FORMAT '/data/backup/full_20211209191000' TAG 'yas_full_backup' PARALLELISM 3;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE FULL FORMAT '+DG0/backup/yfs_full_20211209191000' TAG 'yfs_yas_full_backup' PARALLELISM 3;
```

#### Incremental Backup

An incremental backup refers to the execution of a baseline backup (LEVEL 0) followed by only backing up the incremental data each time (LEVEL 1). When performing LEVEL 1 backups, these can be based on LEVEL 0 (i.e., cumulative incremental backups) or based on previous LEVEL 1 backups (i.e., differential incremental backups).

Specifying the INCREMENTAL keyword indicates an incremental backup.

Incremental backups must adhere to the following usage rules:

- In the same incremental backup chain, consecutive LEVEL 1 incremental backups cannot exceed 1000 times.

- Multiple backup sets in the same incremental backup chain can adopt different compression strategies.

- Multiple backup sets in the same incremental backup chain must adopt consistent encryption strategies (either all encrypted or all unencrypted, with the same password if encrypted), but different encryption algorithms can be used.

- If the storage path of a planned incremental backup set differs from that of its previous backup set, all backup operations must be performed on the same instance; otherwise, the backup operation will fail.

- Recovery requires restoring the baseline backup set and then the incremental backup sets in sequence.

##### LEVEL 0

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE INCREMENTAL LEVEL 0  FORMAT '/data/backup/incr_0_20211209193516';
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE INCREMENTAL LEVEL 0 FORMAT '+DG0/backup/incr_0_20211209193516';
```

##### LEVEL 1

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE INCREMENTAL LEVEL 1 FORMAT '/data/backup/incr_1_20211209193740';
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE INCREMENTAL LEVEL 1 FORMAT '+DG0/backup/incr_1_20211209193740';
```

##### Incremental Backup Based on TAG

When performing incremental backups, the INDEPEND keyword can be specified to generate a backup set for an independent incremental backup chain, and the TAG keyword can be used to specify the unique identifier of the backup set. When executing subsequent LEVEL 1 incremental backups, the constraints are as follows:

- To specify the INDEPEND LEVEL 0 backup set as the baseline, the INDEPEND keyword must be specified.

- To specify a LEVEL 1 backup set in this chain as the baseline, the BASE ON keyword must be specified based on tag_name.

- This feature is only applicable for Standalone Deployment.

When using this type of incremental backup set for recovery, the target backup set can be specified directly based on tag_name.

> **Note**: 
>
> If a source database has executed RESTORE DATABASE to rebuild the database, the baseline for tag-based incremental backups must be rebuilt, meaning a new incremental backup chain must be generated.

***Example*** for Standalone Deployment

```sql
-- LEVEL 0
BACKUP DATABASE INCREMENTAL LEVEL 0  FORMAT '/data/backup/base_incr_0_20211209193516' TAG 'base_incr_0' INDEPEND ;

-- LEVEL 1
BACKUP DATABASE INCREMENTAL LEVEL 1  FORMAT '/data/backup/base_incr_1_20211209193516' TAG 'base_incr_1' BASE ON 'base_incr_0' ;
```

### Compressed Backup

When performing a database backup, the COMPRESSION keyword can be used to specify the compression strategy of the backup set. Options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the options after COMPRESSION are omitted, the default compression algorithm will be ZSTD with a LOW level.

Multiple backup sets in the same incremental backup chain are allowed to adopt different compression strategies.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE COMPRESSION ALGORITHM ZSTD LOW;
```

### Encrypted Backup

When performing a database backup, the ENCRYPTION keyword can be used to specify the encryption strategy for the backup set. Options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; using SM4 by default if omitted.

- The IDENTIFIED BY keyword specifies the key.

Multiple backup sets in the same incremental backup chain must adopt a consistent encryption strategy (either all encrypted or all unencrypted, or all encrypted with the same password), but different encryption algorithms can be used.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE ENCRYPTION AES256 IDENTIFIED BY yas2022;
```

<span id="backup_arch" name="backup_arch" class="yaslink"></span>

## Backup Archive Log Files

### Operation Instructions

- When backing up archive log files, the backup range can be precise to a specific log, but will actually back up the entire file where the target log resides as the minimum unit.

- Database environment preparation before backup:

    - The database instance must be in OPEN state and archiving mode must be enabled.

    - If you need to customize the path to save the backup files, ensure that the path is empty and has sufficient disk space. The FORMAT keyword can be used to customize the backup set file name (including the storage path); if omitted, the default will be $YASDB_DATA/backup/bak_{date}. In YAC/Distributed Cluster Deployment, it can also be specified as a YFS path to store the backup files in shared storage.

### Backup Range

#### Full Backup

A full backup refers to a complete copy of all current archive log files; specifying the ALL keyword indicates a full backup.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL FORMAT '/data/backup/all_20211209191000' TAG 'yas_allarchive_backup' PARALLELISM 3;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP DATABASE FULL FORMAT '+DG0/backup/yfs_all_20211209191000' TAG 'yfs_yas_allarchive_backup' PARALLELISM 3;
```

#### SCN-Based Backup

An SCN-based backup specifies the range of archive log files to be backed up through the log's SCN number. Specific target ranges can be designated using the following keywords:

- FROM SCN n: Indicates that the backup starts from the file where log n is located up to the latest.

- SCN BETWEEN n AND m: Indicates that the backup range consists of archive log files in the [n,m] interval.

- UNTIL SCN n: Indicates that the backup ends at the file where log n is located.

The current SCN information for existing archive log files can be queried through the V$ARCHIVED_LOG view. The FIRST_CHANGE# field indicates the SCN of the first log, while the NEXT_CHANGE# field indicates the SCN of the last log.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG FROM SCN 505411989407080448 FORMAT '/data/backup/scn_20211209191000' TAG 'yas_scnarchive_backup' PARALLELISM 3;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG FROM SCN 505411989407080448 FORMAT '+DG0/backup/yfs_scn_20211209191000' TAG 'yfs_yas_scnarchive_backup' PARALLELISM 3;
```

#### SEQUENCE-Based Backup

A SEQUENCE-based backup specifies the range of archive log files to be backed up via the sequence number of the archive log files. The files specified in the statement must exist. Specific target ranges can be designated using the following keywords:

- FROM SEQUENCE x: Indicates that the backup starts from file x up to the latest.

- SEQUENCE BETWEEN x AND y: Indicates that the backup range consists of all archive log files in the [x,y] interval.

- UNTIL SEQUENCE x: Indicates that the backup ends at file x.

The current sequence numbers and other detailed information for existing archive log files can be obtained through the V$ARCHIVED_LOG view (the SEQUENCE# field).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL SEQUENCE 9 FORMAT '/data/backup/seq_20211209191000' TAG 'yas_seqarchive_backup' PARALLELISM 3;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL SEQUENCE 9 FORMAT '+DG0/backup/yfs_seq_20211209191000' TAG 'yfs_yas_seqarchive_backup' PARALLELISM 3;
```

#### Time-Based Backup

A time-based backup specifies the range of archive log files to be backed up according to time; it will back up the entire archive log file corresponding to a specific log at that time.

The time format can be:

*   `'2021-12-09 19:35:55'`: This format needs to match the DATE_FORMAT parameter specified for the current session.

*   `TO_DATE('2021-12-09 19:35:55','yyyy-mm-dd hh24:mi:ss')`: Using the TO_DATE function for conversion.

*   `TO_TIMESTAMP('2021-12-09 19:35:55','yyyy-mm-dd hh24:mi:ss')`: Using the TO_TIMESTAMP function for conversion.

Specific target ranges can be designated using the following keywords:

- FROM TIME t: Indicates that the backup starts from the first log in the archive log file corresponding to time t up to the latest.

- TIME BETWEEN t1 AND t2: Indicates that the backup range consists of all log records in the archive log files during the period [t1,t2].

- UNTIL TIME t: Indicates that the backup ends at the last log in the archive log file corresponding to time t.

The earliest time cannot be earlier than the installation time of the current environment, and the latest time can be set in the future but the actual backup content is limited to the last log's SCN corresponding to the maximum archive log file's sequence number.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL TIME TO_DATE('2024-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') FORMAT '/data/backup/time_20211209191000' TAG 'yas_timearchive_backup' PARALLELISM 3;
```

***Example*** for YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL TIME TO_DATE('2024-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') FORMAT '+DG0/backup/yfs_time_20211209191000' TAG 'yfs_yas_timearchive_backup' PARALLELISM 3;
```

### Compressed Backup

When performing archive backups, the COMPRESSION keyword can be used to specify the compression strategy for the backup set. Options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the options after COMPRESSION are omitted, the default compression algorithm will be ZSTD with a LOW level.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL COMPRESSION;
```

### Encrypted Backup

When performing archive backups, the ENCRYPTION keyword can be used to specify the encryption strategy for the backup set. Options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; using SM4 by default if omitted.

- The IDENTIFIED BY keyword specifies the key.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL ENCRYPTION IDENTIFIED BY 12345;
```
