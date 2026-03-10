## General Description

BACKUP ARCHIVELOG is used to perform a backup of the current database archive log files.

Backup of the archive log files can only be performed when the database is in OPEN status, and archiving mode must be enabled (adjust archiving mode using the [ALTER DATABASE](ALTER DATABASE) statement).

This statement is not applicable to ISC Distributed Cluster Deployment.

The DBA_ARCHIVE_BACKUPSET view can be queried for information about the generated backup sets, and the V$BACKUP_PROGRESS view can be queried for information about the backup process.

For detailed operational descriptions on backup recovery, please refer to [Backup and Recovery](../../All Manuals/数据库管理/备份与恢复/00备份与恢复).

## Statement Definition

**backup archivelog::=**

```ebnf+diagram
syntax::= BACKUP ARCHIVELOG archivelogRangeSpecifier [backupCommonSpecifier]
```

**[archive_log_range_specifier](#archivelograngespecifier)::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) 
```

**[backup_common_specifier](#backupcommonspecifier)::=**

```ebnf+diagram
syntax::= ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|(TAG tag_name)|
PARALLELISM integer|
SECTION SIZE size_clause)
{" " ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|(TAG tag_name)|
PARALLELISM integer|
SECTION SIZE size_clause)}
```
<span id="archivelograngespecifier" name="archivelograngespecifier" class="yaslink"></span>

### 1. archivelogRangeSpecifier

Used to specify the range of archive log files to be backed up.

If any specified archive log files do not exist within the range (for example, the file has been deleted, or the archive log file corresponding to a sequence number has not yet been generated), the backup operation will fail and prompt the error code YAS-02544.

#### 1.1. SCN

Used to specify the SCN range of the archive log files to be backed up. SCN is a 64-bit unsigned integer.

The V$ARCHIVED_LOG view can be used to obtain the SCN of the first log (FIRST_CHANGE# field), the SCN of the last log (NEXT_CHANGE# field), and other details for the currently existing archive log files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG SCN BETWEEN 505411989407080448 AND 505412089999663104 FORMAT 'SCNRANGE' TAG 'SCNRANGE' COMPRESSION;
```

#### 1.2. SEQUENCE

Used to specify the sequence number range of the archive log files to be backed up. The sequence number is a 32-bit unsigned integer.

The GV$ARCHIVED_LOG/V$ARCHIVED_LOG view can be used to obtain the sequence numbers (SEQUENCE# field) and other details for the currently existing archive log files.

In YAC/Distributed Cluster Deployment, it is recommended to use SCN or TIME to specify the backup range for archive log files. If using a sequence number, executing `BACKUP ARCHIVELOG SEQUENCE ...` once will only back up archive log files from one instance (default: `INST_ID=1`). To complete backup of archive log files across all instances in the cluster, you must repeatedly execute this statement and specify different instance IDs using the `THREAD` keyword.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 FORMAT 'ASNRANGE' TAG 'ASNRANGE' PARALLELISM 8 SECTION SIZE 128M;
```

#### 1.3. TIME

Used to specify the time range of the archive log files to be backed up. The earliest time cannot be earlier than the installation time of the current environment, and the latest time can be set to a future time, but the actual backup content is limited to the SCN of the last log in the file corresponding to the maximum archived file sequence number.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG TIME BETWEEN TO_DATE('2023-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') AND TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss') FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;
```

#### 1.4. FROM

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the starting point for backing up the archive log files.

When specifying the FROM clause to back up archive log files, the endpoint is the last log in the currently existing maximum archived file sequence number, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files.

If the specified starting point is greater than (or later than) the endpoint, the error code YAS-02541 will be displayed, indicating no matching archived files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG FROM SCN 505412089999663104 FORMAT 'FROMSCN' TAG 'FROMSCN' COMPRESSION;

BACKUP ARCHIVELOG FROM SEQUENCE 10 FORMAT 'FROMASN' TAG 'FROMASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG FROM TIME TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'FROMTIME' TAG 'FROMTIME' COMPRESSION;
```

#### 1.5. BETWEEN … AND …

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the range of archive log files to be backed up.

When specifying the BETWEEN ... (starting point) AND ... (endpoint) clause to back up archive log files, both the starting point and the endpoint are the specified values, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files and choose the starting and ending points as needed.

If the specified starting point is greater than (or later than) the endpoint, the error code YAS-02541 will be displayed, indicating no matching archived files.

#### 1.6. UNTIL

In the archiving backup statement, it is used in conjunction with SCN, SEQUENCE, or TIME to specify the endpoint for backing up the archive log files.

When specifying the UNTIL clause to back up archive log files, the starting point is the first log in the currently existing minimum archived file sequence number, and the V$ARCHIVED_LOG view can be used to obtain relevant information about the currently existing archive log files.

If the specified endpoint is less than (or earlier than) the starting point, the error code YAS-02541 will be displayed, indicating no matching archived files.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG UNTIL SCN  505412089999663104 FORMAT 'UNTILSCN' TAG 'UNTILSCN' COMPRESSION;

BACKUP ARCHIVELOG UNTIL SEQUENCE 10 FORMAT 'UNTILASN' TAG 'UNTILASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG UNTIL TIME TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'UNTILTIME' TAG 'UNTILTIME' COMPRESSION;
```
<span id="backupcommonspecifier" name="backupcommonspecifier" class="yaslink"></span>

### 2. backupCommonSpecifier

Used to specify common configurations for the backup operation such as compression, encryption, and parallelism.

#### 2.1. COMPRESSION

This statement is used to compress the backup set. When options following COMPRESSION are omitted, it indicates backup data is compressed using the ZSTD algorithm with LOW compression level.

**Compression Algorithm**

The ALGORITHM keyword specifies the compression algorithm. YashanDB provides the following two compression algorithms for users to choose from:

- ZSTD: Can provide higher compression rates.
- LZ4: Can provide faster compression rates.

**Compression Level**

When specifying the compression algorithm, the compression level can also be specified. If omitted, it defaults to LOW level.

The compression levels include HIGH, MEDIUM, and LOW, where HIGH indicates the highest compression rate (lowest compression speed), and LOW indicates the lowest compression rate (highest compression speed).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL COMPRESSION;
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 COMPRESSION ALGORITHM lz4 HIGH;
```

#### 2.2. ENCRYPTION

This statement is used to encrypt the backup set.

**Encryption Algorithm**

YashanDB provides the following four encryption algorithms for selection. If not specified, it defaults to AES128 encryption algorithm:

- AES128
- AES192
- AES256
- SM4

**IDENTIFIED BY**

The key that must be entered when specifying encrypted backup is constrained by the same rules as the user password rules, as described in [CREATE USER](CREATE USER).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
BACKUP ARCHIVELOG ALL ENCRYPTION IDENTIFIED BY 12345;
```

#### 2.3. FORMAT

This statement is used to specify the file name of the generated backup set. If omitted, it defaults to `$YASDB_DATA/backup/bak_{date}`.

When the file name does not specify a path, it defaults to creation in the database's $YASDB_DATA/backup folder.

In YAC/Distributed Cluster Deployment, by specifying the [YFS](../../All Manuals/数据库管理/存储管理/集群文件系统管理/00集群文件系统管理) path, data can be backed up to shared storage; when specified as a regular disk path, the data is backed up to the server where the instance is located.

#### 2.4. TAG

This statement is used to specify an alias for the backup set. The maximum length is 64 (including the terminating character '\\0').

#### 2.5. PARALLELISM

This statement is used to specify the parallelism of multi-threaded backups, with a range of [1,8]. If omitted, it defaults to 2.

#### 2.6. SECTION SIZE

This statement is used to specify the size of file chunks. Files larger than this value will be split into multiple smaller files for backup. The range is [128M,32T] and if omitted, it defaults to the system-calculated optimal value. If the specified default file size is not a multiple of 1M, it will be rounded down to align with 1M.

> **Note**: 
>
> Please choose an appropriate chunk size based on the actual data file size. Assuming the maximum size of a single data file in YashanDB is MAXSIZE, the recommended chunk size should be less than (MAXSIZE/(PARALLELISM*2)), and should not exceed 4G, where PARALLELISM is the specified parallelism for the backup, which can effectively improve backup efficiency.