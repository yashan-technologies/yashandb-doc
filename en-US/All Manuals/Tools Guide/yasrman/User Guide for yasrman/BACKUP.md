BACKUP can back up the primary database/standby database, tablespace, and archive files. It also partially supports backup functionality for YACs and distributed clusters.

In Standalone Deployment and YAC Deployment, when the BACKUP operation is executed, the system will automatically perform a pre-check of the backup environment first. The checks include whether the backup path is accessible and whether there is sufficient free disk space at the destination address. If the checks pass, the backup will proceed; if the checks fail, the operation will exit directly.

## Common Backup Attributes

<span id="backupCommonSpecifier" name="backupCommonSpecifier" class="yaslink"></span>
**backupCommonSpecifier::=**

```ebnf+diagram
syntax::= ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|
PARALLELISM integer|
SECTION SIZE size_clause|
SKIP VALIDATE|
DEST (server|client)|
PARAMS xbsa_parameter)
{" " ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|
PARALLELISM integer|
SECTION SIZE size_clause|
SKIP VALIDATE|
DEST (server|client)|
PARAMS xbsa_parameter)}
```

### COMPRESSION

The compression attribute is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

### ENCRYPTION

The encryption attribute is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

### FORCE

Whether to force the backup is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

### FORMAT

The name (including path) of the backup set file is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

When the format does not specify a path, the following default paths are used:

- For backups on the server (DEST SERVER), the backup set is stored by default in the $YASDB_DATA/backup folder. In YAC Deployment, it can also be specified as a YFS path to store the backup set files in shared storage.

- For backups on the client side (DEST CLIENT), the backup set is stored by default in the catalog/backup folder.

> **Note**: 
>
> In a distributed cluster, when multiple nodes are deployed on the same server, it is recommended to use relative paths for FORMAT, otherwise, a path conflict may occur, leading to backup failure.

### PARALLELISM

The degree of parallelism is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

### SECTION SIZE

The file segment specification is defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

> **Note**: 
>
> Please select an appropriate segment size based on the actual data file size. Assuming the maximum size of a single data file in YashanDB is MAXSIZE, the recommended segment size should be less than (MAXSIZE/(PARALLELISM*2)), and the maximum should not exceed 4G, where PARALLELISM is the specified degree of parallelism for backup, which can effectively improve backup efficiency.

### DEST

Specifies the storage location of the backup set; it can be omitted, and defaults to SERVER.

- CLIENT indicates storage on the client side, i.e., the backup set is saved on the server where *yasrman* exists.

- SERVER indicates backup to the database server side, i.e., the backup set is stored on the database server (in YACs, the YFS path can be specified via FORMAT to store the backup set files in shared storage).

In ISC Distributed Cluster Deployment, only SERVER can be used.

When performing an incremental backup, the DEST option of the backup command for the same baseline must be consistent with the baseline, meaning that all incremental backup sets must either be on the client side or the server side; otherwise, the recovery process may fail due to the inability to find the baseline backup set.

### PARAMS

This statement is used to interface with third-party backup software, specifying the configuration parameters for the XBSA interface to perform remote streaming backups.

When using this statement, the storage location of the backup set must be specified as the client side (DEST CLIENT), and the FORMAT parameter will be ignored.

This statement cannot be used in ISC Distributed Cluster Deployment.

The format is as follows:
PARAMS 'XBSA_LIBRARY=xbsa_library_path, TOKEN=token_value, ENV=(key1=val1,key2=val2,...)'

- xbsa_library_path: The absolute path of the XBSA dynamic link library, currently only supported is the dynamic link library for Linux.
- token_value: The token parameter for the BSAInit interface.
- keyX, valX: The env parameters for the BSAInit interface; the total length of all key-value pairs must be less than 4096.

***Example*** for Standalone Deployment and YAC Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_full_1' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234561)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_0' incremental level 0 compression algorithm lz4 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_1' incremental level 1 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all2' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog
```

## Full Database Backup

```ebnf+diagram
syntax::= BACKUP (CLUSTER|DATABASE) TAG tag_name 
((FULL|(INCREMENTAL LEVEL integer [CUMULATIVE]))|backupCommonSpecifier)
{ " " ((FULL|(INCREMENTAL LEVEL integer [CUMULATIVE]))|backupCommonSpecifier)} [FORCE]
```

### CLUSTER|DATABASE

Used to specify different deployment modes for database backup.

- Use the DATABASE keyword to indicate a backup for a standalone or YAC database.

- Use the CLUSTER keyword for database backups in ISC Distributed Cluster Deployment, indicating backup for all CN nodes, DN primary nodes, and MN primary nodes in a distributed cluster. If any node's backup fails during the execution process, all node backups will fail, and this command will throw an error.

### FULL

Full backup, defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE.html#full).

### INCREMENTAL LEVEL

Incremental backup, defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE.html#incremental).

### TAG

A unique identifier for the backup set used to distinguish backup sets, defined and described the same as in the related description in [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP DATABASE).

### backupCommonSpecifier

Common backup attributes such as compression and encryption, please refer to [Common Backup Attributes](#backupCommonSpecifier).

***Example*** for Standalone Deployment and YAC Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 0 compression encryption aes128 identified by 123456 format 'incr_base' tag 'incr_base' dest server;" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 1 compression algorithm lz4 encryption aes128 identified by 123456 format 'incr_1' tag 'incr_1' dest server;" -D /home/yashan/catalog
```

> **Note**: 
>
> The *yasrman* backup recovery commands in YAC Deployment are exactly the same as those in Standalone Deployment, and the target nodes for backup can specify any live node.

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 0 compression format 'cluster_incr_base' tag 'cluster_incr_base';" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 1 compression algorithm zstd format 'cluster_incr_1' tag 'cluster_incr_1';" -D /home/yashan/catalog
```

## Tablespace Backup

```ebnf+diagram
syntax::= BACKUP TABLESPACE ((TBS_NAME) {"," (TBS_NAME)}) TAG tag_name 
(backupCommonSpecifier)
{ " " (backupCommonSpecifier)}
```

Constraints for tablespace backup are as follows:

- Applicable only to the primary database in Standalone Deployment.

- Cannot back up temporary tablespaces, UNDO tablespaces, and corrupted tablespaces.

- Backup operations are mutually exclusive with file additions/deletions, and tablespace offline/online operations.

- The tablespace backup does not include archive files and does not back up BUCKET files.

- If the restoration of a tablespace file fails, no backup operations can be performed on that tablespace file.

### TABLESPACE

This keyword is used to specify tablespace backups, i.e., backing up all data files under one or more specified tablespaces in the database.

TBS_NAME indicates the tablespace name that must be manually specified, and this tablespace must exist in the target database.

### backupCommonSpecifier

Specify backup attributes as needed.

***Example*** for Standalone Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user tag 'user_bak'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user,system tag 'sys_bak'" -D /home/yashan/catalog
```

## Archive Log Files Backup

```ebnf+diagram
syntax::= BACKUP ARCHIVELOG archivelogRangeSpecifier (TAG tag_name [backupCommonSpecifier])
```

**archivelogRangeSpecifier::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) [NOT BACKED UP integer TIMES]
```

Archive log files backup is not applicable to ISC Distributed Cluster Deployment.

Before backing up archive log files, ensure that the databases on all nodes are in OPEN status and archiving is enabled.

### ARCHIVELOG

This keyword indicates that the backup is performed on archive log files for the target database.

### SEQUENCE|SCN|TIME

Specify the range of archive log files to be backed up based on SEQUENCE, SCN, or time, defined and described the same as in the related description in [BACKUP ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP ARCHIVELOG).

> **Note**: 
>
> - If specifying time, the format must ensure it is in yyyy-mm-dd hh24:mi:ss format.
>
> - If there are no archive files in the specified SEQUENCE range, an error code YAS-02544 will be reported.

### FROM|BETWEEN … AND …|UNTIL

Used in conjunction with SEQUENCE, SCN, or time to specify the start/end points for backup, defined and described the same as in the related description in [BACKUP ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP ARCHIVELOG).

### NOT BACKED UP integer TIMES

Specifies that during the backup of archive logs, archive files that have been backed up at least integer times will be filtered (not backed up).

> **Note**: 
>
> If the specified backup count leads to a discontinuous range of archive logs for the final backup, an error message will be reported, and this backup task will be interrupted.

```shell
# Backup all Archive Log Files, skipping archive files that have been backed up twice
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all' not backed up 2 times" -D /home/yashan/catalog
```

### backupCommonSpecifier

Common backup attributes such as compression and encryption, please refer to [Common Backup Attributes](#backupCommonSpecifier).

***Example*** for Standalone Deployment and YAC Deployment

### SKIP VALIDATE

The default behavior for ISC Distributed Cluster Deployment mode is to skip checks; other deployment modes default to checking.

This keyword specifies skipping the pre-checks for the backup.

The pre-check items include whether the storage path for the backup set is accessible, whether there is available disk space, etc. By performing pre-checks, the risk of backup failure due to disk space and other issues can be effectively avoided, thereby improving backup efficiency and saving time.

```shell
# Backup all Archive Log Files
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all'" -D /home/yashan/catalog

# Select backup range based on sequence (10 ~ 20)
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog sequence between 10 and 20 tag 'sequence_10_20' format 'sequence_10_20'" -D /home/yashan/catalog

# Choose backup range based on time and perform a compressed backup
$ yasrman sys/********@192.168.1.2:1688 -c "BACKUP ARCHIVELOG TIME BETWEEN '2023-10-28 08:14:01' AND '2023-11-28 11:14:01'  FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;" -D /home/yashan/catalog
```
