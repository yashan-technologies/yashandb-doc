## General Description

BACKUP ARCHIVELOG is used to perform a backup of the current database archive log files.

Backup of the archive log files can only be performed when the database is in OPEN status, and archiving mode must be enabled (adjust archiving mode using the [ALTER DATABASE](ALTER DATABASE) statement).

This statement is not applicable to ISC Distributed Cluster Deployment.

The DBA_ARCHIVE_BACKUPSET view can be queried for information about the generated backup sets, and the v$BACKUP_PROGRESS view can be queried for information about the backup process.

For detailed operational descriptions on backup recovery, please refer to [Backup and Recovery](../../../Database Administration/Backup and Recovery/00Backup and Recovery).

## Statement Definition

**backup archivelog::=**

```ebnf
= BACKUP ARCHIVELOG archivelogRangeSpecifier [backupCommonSpecifier].
```

**[archive_log_range_specifier](#archivelograngespecifier)::=**

```ebnf
= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) .
```

**[backupCommonSpecifier](#backupcommonspecifier)::=**

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
<span id="archivelograngespecifier" name="archivelograngespecifier"></span>

### archivelogRangeSpecifier

Used to specify the range of archive log files to be backed up.

If any specified archive log files do not exist within the range (for example, the file has been deleted, or the archive log file corresponding to a sequence number has not yet been generated), the backup operation will fail and prompt the error code YAS-02544.

#### SCN

Used to specify the SCN range of the archive log files to be backed up. SCN is a 64-bit unsigned integer.

The V$ARCHIVED_LOG view can be used to obtain the SCN of the first log (FIRST_CHANGE# field), the SCN of the last log (NEXT_CHANGE# field), and other details for the currently existing archive log files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG SCN BETWEEN 505411989407080448 AND 505412089999663104 FORMAT 'SCNRANGE' TAG 'SCNRANGE' COMPRESSION;
```

#### SEQUENCE

Used to specify the sequence number range of the archive log files to be backed up. The sequence number is a 32-bit unsigned integer.

The GV$ARCHIVED_LOG/V$ARCHIVED_LOG view can be used to obtain the sequence numbers (SEQUENCE# field) and other details for the currently existing archive log files.

In YAC/Distributed Cluster Deployment, it is recommended to use SCN or TIME to specify the backup range for archive log files. If using a sequence number, executing `BACKUP ARCHIVELOG SEQUENCE ...` once will only back up archive log files from one instance (default: `INST_ID=1`). To complete backup of archive log files across all instances in the cluster, you must repeatedly execute this statement and specify different instance IDs using the `THREAD` keyword.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 FORMAT 'ASNRANGE' TAG 'ASNRANGE' PARALLELISM 8 SECTION SIZE 128M;
```

#### TIME

Used to specify the time range of the archive log files to be backed up. The earliest time cannot be earlier than the installation time of the current environment, and the latest time can be set to a future time, but the actual backup content is limited to the SCN of the last log in the file corresponding to the maximum archived file sequence number.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG TIME BETWEEN to_date('2023-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') AND to_date('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss') FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;
```

#### FROM

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the starting point for backing up the archive log files.

When specifying the FROM clause to back up archive log files, the endpoint is the last log in the currently existing maximum archived file sequence number, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files.

If the specified starting point is greater than (or later than) the endpoint, the error code YAS-02541 will be displayed, indicating no matching archived files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG FROM SCN 505412089999663104 FORMAT 'FROMSCN' TAG 'FROMSCN' COMPRESSION;

BACKUP ARCHIVELOG FROM SEQUENCE 10 FORMAT 'FROMASN' TAG 'FROMASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG FROM TIME to_date('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'FROMTIME' TAG 'FROMTIME' COMPRESSION;
```

#### BETWEEN … AND …

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the range of archive log files to be backed up.

When specifying the BETWEEN ... (starting point) AND ... (endpoint) clause to back up archive log files, both the starting point and the endpoint are the specified values, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files and choose the starting and ending points as needed.

If the specified starting point is greater than (or later than) the endpoint, the error code YAS-02541 will be displayed, indicating no matching archived files.

#### UNTIL

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the endpoint for backing up the archive log files.

When specifying the UNTIL clause to back up archive log files, the starting point is the first log in the currently existing minimum archived file sequence number, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files.

If the specified endpoint is less than (or earlier than) the starting point, the error code YAS-02541 will be displayed, indicating no matching archived files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL SCN  505412089999663104 FORMAT 'UNTILSCN' TAG 'UNTILSCN' COMPRESSION;

BACKUP ARCHIVELOG UNTIL SEQUENCE 10 FORMAT 'UNTILASN' TAG 'UNTILASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG UNTIL TIME to_date('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'UNTILTIME' TAG 'UNTILTIME' COMPRESSION;
```
<span id="backupcommonspecifier" name="backupcommonspecifier"></span>

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
BACKUP ARCHIVELOG ALL COMPRESSION;
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 COMPRESSION ALGORITHM lz4 HIGH;
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
BACKUP ARCHIVELOG ALL ENCRYPTION IDENTIFIED BY 12345;
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



#### SECTION SIZE



This statement is used to specify the file shard size; files exceeding this size will be split into multiple smaller files for backup. The range is [128M,32T], and if omitted, the default is the system-calculated optimal value.

Configuration recommendations are as follows:

- It is recommended to specify the shard specification based on the actual data file size.

  Assuming the maximum size of a single data file in YashanDB is MAXSIZE, it is recommended that the shard specification be less than MAXSIZE/(PARALLELISM*2).

- It is recommended that the shard specification be less than 4G.

If the specified value is not an integer multiple of 1M, it will be rounded down to the nearest 1M alignment.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL COMPRESSION PARALLELISM 8 SECTION SIZE 128M;
```
