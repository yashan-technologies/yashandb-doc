BACKUP can back up the primary database/standby database, tablespace, and archive files. It also partially supports backup functionality for YACs and distributed clusters.

In Standalone/YAC/Distributed Cluster Deployment, when the BACKUP operation is executed, the system will automatically perform a pre-check of the backup environment first. The checks include whether the backup path is accessible and whether there is sufficient free disk space at the destination address. If the checks pass, the backup will proceed; if the checks fail, the operation will exit directly.

<span id="backupCommonSpecifier" name="backupCommonSpecifier"></span>

## Common Backup Attributes

**backupCommonSpecifier::=**

```ebnf
= (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
DEST (SERVER|CLIENT)|
(FORMAT backup_path)|
SECTION SIZE size_clause|
SKIP VALIDATE|
PARAMS xbsa_parameter)
{" " (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
DEST (SERVER|CLIENT)|
(FORMAT backup_path)|
SECTION SIZE size_clause|
SKIP VALIDATE|
PARAMS xbsa_parameter)}.
```

### COMPRESSION



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



### ENCRYPTION



Specifies the encryption attributes of the backup set, including the encryption algorithm and key. If the encryption algorithm is not explicitly specified, the default algorithm is SM4.

Each backup set in incremental backups must either all be encrypted or all not be encrypted, and the keys of each backup set must be consistent.

**Encryption Algorithm**

The following encryption algorithms are supported:

- AES128

- AES192
- AES256
- SM4


**Key**

The encryption key for encrypted backups is specified via the IDENTIFIED BY keyword, and its constraints follow the same rules as the [database user password rules](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER.md#password).



### PARALLELISM



Specifies the parallel degree for multithreaded backup, with a range of [1,16]; if omitted, the default is 2.



### DEST

Specifies the backup destination — that is, the storage location for the backup set. This can be omitted, with the default being `SERVER`.

- SERVER: Indicates backing up to the database server side — that is, the backup set is saved on the server where the database resides (in YAC/Distributed Cluster Deployment, the `FORMAT` can be used to specify the YFS path to store the backup set files on shared storage).

- CLIENT: Indicates backing up to the client side, i.e., the backup set is saved on the server where *yasrman* exists.

In ISC Distributed Cluster Deployment, only SERVER can be used.

When performing an incremental backup, the DEST option of the backup commands for the same baseline must be consistent with that of the baseline. That is, the backup sets in the same incremental backup group must be stored on the same device. Otherwise, an error may occur during restoration due to the inability to find the baseline backup set.

### FORMAT

Specifies the backup set file name (can include a path). If omitted, the default file name is `bak_{date}`, and the default storage path depends on the backup destination:

- Backup to server (DEST SERVER): The backup set is stored by default in the `$YASDB_DATA/backup` directory.

- Backup to client (DEST CLIENT): The backup set is stored by default in the `catalog/backup` directory.

In YAC/Distributed Cluster Deployment, you can specify a [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) path to store the backup set on shared storage; if a local path is specified, the backup set will be saved on the server where the instance resides.

> **Note**: 
>
> In a distributed cluster, when multiple nodes are deployed on the same server, it is recommended to use relative paths for FORMAT, otherwise, a path conflict may occur, leading to backup failure.

### SECTION SIZE



This statement is used to specify the file shard size; files exceeding this size will be split into multiple smaller files for backup. The range is [128M,32T], and if omitted, the default is the system-calculated optimal value.

Configuration recommendations are as follows:

- It is recommended to specify the shard specification based on the actual data file size.

  Assuming the maximum size of a single data file in YashanDB is MAXSIZE, it is recommended that the shard specification be less than MAXSIZE/(PARALLELISM*2).

- It is recommended that the shard specification be less than 4G.

If the specified value is not an integer multiple of 1M, it will be rounded down to the nearest 1M alignment.



### SKIP VALIDATE

Specifies whether to skip backup pre-checks. If omitted, pre-checks are skipped by default in ISC Distributed Cluster Deployment; for all other deployments, pre-checks are performed by default.

The pre-check items include whether the storage path for the backup set is accessible, whether there is available disk space, etc. By performing pre-checks, the risk of backup failure due to disk space and other issues can be effectively avoided, thereby improving backup efficiency and saving time.

### PARAMS

This statement is used to interface with third-party backup software, specifying the configuration parameters for the XBSA interface to perform remote streaming backups.

When using this statement, the storage location of the backup set must be specified as the client side (DEST CLIENT), and the FORMAT parameter will be ignored.

This statement cannot be used in ISC Distributed Cluster Deployment.

The format is as follows:
PARAMS 'XBSA_LIBRARY=xbsa_library_path, TOKEN=token_value, ENV=(key1=val1,key2=val2,...)'

- xbsa_library_path: The absolute path of the XBSA dynamic link library, currently only supported is the dynamic link library for Linux.
- token_value: The token parameter for the BSAInit interface.
- keyX, valX: The env parameters for the BSAInit interface; the total length of all key-value pairs must be less than 4096.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_full_1' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234561)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_0' incremental level 0 compression algorithm lz4 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_1' incremental level 1 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all2' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog
```

## Full Database Backup

```ebnf
= BACKUP dbObjectSpecifier TAG tag_name 
([FULL]|(INCREMENTAL LEVEL integer [CUMULATIVE])) 
[backupCommonSpecifier] 
[FORCE].
```

**dbObjectSpecifier::=**

```ebnf
= DATABASE [ROOT]|PLUGGABLE DATABASE (pdb_name {"," pdb_name}) |CLUSTER.
```

### dbObjectSpecifier

Specify backup objects via keywords:

| Applicable Scenario         | Keyword      | Functional Description     |
|-----------------|-------------|-------------------------|
| Non-CDB | DATABASE  | Backs up the entire database in Standalone Deployment or YAC Deployment. |
| Non-CDB | CLUSTER | Backs up the entire ISC Distributed Cluster Deployment database (all CN, DN master nodes, and MN master nodes). If any node backup fails, all backups abort and the command throws an error. |
| CDB | DATABASE | Backs up the entire database or a single PDB based on connection:<br/>* Connected to the CDB root: Backs up the CDB root, PDB seed, and all PDBs<br/>* Connected to a PDB: Backs up that PDB |
| CDB | DATABASE ROOT | Backs up the CDB root. Must connect to the CDB root to execute.  |
| CDB | PLUGGABLE DATABASE | Backs up the specified PDB(s) or PDB seed. Requires a user with backup and connection privileges for the PDB, connected to the CDB root. |

### FULL

Specifies the backup method as a full backup; if omitted, the default is full backup.  

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data.

### INCREMENTAL

Specifies the backup method as incremental backup.

When using incremental backup, backup efficiency is higher and disk space is saved. During recovery, you must start from the baseline backup set and sequentially restore all incremental backups.

#### LEVEL integer

 

Used to specify the incremental backup level, it cannot be omitted, and the value can be LEVEL 0 or LEVEL 1.

- LEVEL 0 indicates the first incremental backup (backup scope is equivalent to full backup, but a physical identifier distinguishing it from the full backup is added in the backup summary file).

- LEVEL 1 indicates a non-first incremental backup.

 

#### CUMULATIVE

 

For LEVEL 1 incremental backups, explicitly specifying CUMULATIVE indicates that the incremental backup method is cumulative; otherwise, it is a normal incremental backup.

*   Normal Incremental Backup: The baseline (LSN) of a normal incremental backup is the previous incremental backup (either level 0 or level 1). To recover from this type of incremental backup set, at least one other incremental backup set is also needed.

*   Cumulative Incremental Backup: The baseline (LSN) of a cumulative incremental backup is the most recent level 0 incremental backup. To recover from this type of incremental backup set, only one other incremental backup set is required.

 

### TAG



Specifies the unique identifier for the backup set, used to distinguish between backup sets. The identifier name maximum length is 64 (including the null terminator '\\0').



### backupCommonSpecifier

Common backup attributes such as compression and encryption, please refer to [Common Backup Attributes](#backupCommonSpecifier).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 0 compression encryption aes128 identified by 123456 format 'incr_base' tag 'incr_base' dest server;" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 1 compression algorithm lz4 encryption aes128 identified by 123456 format 'incr_1' tag 'incr_1' dest server;" -D /home/yashan/catalog
```

***Example*** for CDB

```shell
# Connect to the CDB root and back up the entire database (CDB and all PDBs)
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'cdb_full' format 'cdb_full'" -D /home/yashan/catalog

# Connect to the CDB root and back up a specified PDB
$ yasrman sys/********@192.168.1.2:1688 -c "backup pluggable database pdb1,pdb2 format 'part_pdb' tag 'part_pdb' dest server;" -D /home/yashan/catalog
```

> **Note**: 
>
> The *yasrman* backup recovery commands in YAC/Distributed Cluster Deployment are exactly the same as those in Standalone Deployment, and the target nodes for backup can specify any live node.

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 0 compression format 'cluster_incr_base' tag 'cluster_incr_base';" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 1 compression algorithm zstd format 'cluster_incr_1' tag 'cluster_incr_1';" -D /home/yashan/catalog
```

### FORCE

 

Specifies whether to force a full backup on a read-only database, such as the standby database in a primary-standby deployment or a database in an abnormal state.

Forced backup does not record system tables. When backing up a standby database, the standby database does not need to be connected to the primary database.

If a backup is executed when the database is in an abnormal state, the FORCE keyword must be specified.

 

## Tablespace Backup

```ebnf
= BACKUP TABLESPACE (TBS_NAME {"," TBS_NAME}) TAG tag_name 
(backupCommonSpecifier)
{ " " (backupCommonSpecifier)}.
```

Constraints for tablespace backup are as follows:

- Only applicable to Standalone Deployment. For non-CDBs, connect to the primary database to execute operations. For CDBs, connect to the primary PDB.

- Must be backed up by directly connecting to the database or PDB to which the target tablespace belongs.

- Cannot back up temporary tablespaces, UNDO tablespaces, and corrupted tablespaces.

- Backup operations are mutually exclusive with file additions/deletions, and tablespace offline/online operations.

- The tablespace backup does not include archive files and does not back up BUCKET files.

- If the restoration of a tablespace file fails, no backup operations can be performed on that tablespace file.

### TABLESPACE

This keyword is used to specify tablespace backups, i.e., backing up all data files under one or more specified tablespaces in the database.

TBS_NAME indicates the tablespace name that must be manually specified, and this tablespace must exist in the target database.

### TAG



Specifies the unique identifier for the backup set, used to distinguish between backup sets. The identifier name maximum length is 64 (including the null terminator '\\0').



### backupCommonSpecifier

Specify backup attributes as needed.

***Example*** for Standalone Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user tag 'user_bak'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user,system tag 'sys_bak'" -D /home/yashan/catalog
```

## Archive Log Files Backup

```ebnf
= BACKUP archivelogObjectSpecifier archivelogRangeSpecifier (TAG tag_name [backupCommonSpecifier]).
```

**archivelogObjectSpecifier::=**

```ebnf
= ARCHIVELOG [ROOT]| PLUGGABLE ARCHIVELOG (pdb_name {"," pdb_name}).
```

**archivelogRangeSpecifier::=**

```ebnf
= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) [NOT BACKED UP integer TIMES].
```

Archive log files backup is not applicable to ISC Distributed Cluster Deployment.

Before backing up archive log files, ensure that the databases on all nodes are in OPEN status and archiving is enabled.

### archivelogObjectSpecifier

Specify backup objects via keywords:

| Applicable Scenario         | Keyword      | Functional Description     |
|--------------------|-------------|-------------------------|
| Non-CDB | ARCHIVELOG  | Backs up archive log files for Standalone Deployment or YAC Deployment. |
| CDB | ARCHIVELOG | Backs up archive log files for the entire database or a single PDB, depending on the connected object:<br/>* When connected to the CDB root: Backs up archive logs for the CDB root, PDB seed, and all PDBs, but only supports specifying the backup ranges using `ALL`, `UNTIL TIME`, or `UNTIL SCN`. <br/>* When connected to a PDB: Backs up archive logs for that PDB. |
| CDB | ARCHIVELOG ROOT | Backs up archive logs for the CDB root. Must connect to the CDB root to execute. <br/>Only supports specifying the backup ranges using `ALL`, `UNTIL TIME`, or `UNTIL SCN`. |
| CDB | PLUGGABLE ARCHIVELOG | Backs up archive logs for a specified PDB. Must be executed by a user with backup and connection privileges for the specified PDB, connected to the CDB root. Only supports specifying the backup ranges using `ALL`, `UNTIL TIME`, or `UNTIL SCN`. |

### SEQUENCE|SCN|TIME

Specify the range of archive log files to be backed up based on SEQUENCE, SCN, or time, defined and described the same as in the related description in [BACKUP ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP ARCHIVELOG).

> **Note**: 
>
> - If specifying time, the format must ensure it is in yyyy-mm-dd hh24:mi:ss format.
>
> - If there are no archive files in the specified SEQUENCE range, an error code YAS-02544 will be reported.
>
> - In a CDB, when directly connected to the CDB root, archive log ranges can **only** be specified using `ALL`, `UNTIL TIME`, or `UNTIL SCN`. No such restrictions apply when directly connected to a PDB.


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

### TAG



Specifies the unique identifier for the backup set, used to distinguish between backup sets. The identifier name maximum length is 64 (including the null terminator '\\0').



### backupCommonSpecifier

Common backup attributes such as compression and encryption, please refer to [Common Backup Attributes](#backupCommonSpecifier).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Backup all Archive Log Files
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all'" -D /home/yashan/catalog

# Select backup range based on sequence (10 ~ 20)
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog sequence between 10 and 20 tag 'sequence_10_20' format 'sequence_10_20'" -D /home/yashan/catalog

# Choose backup range based on time and perform a compressed backup
$ yasrman sys/********@192.168.1.2:1688 -c "BACKUP ARCHIVELOG TIME BETWEEN '2025-10-28 08:14:01' AND '2025-11-28 11:14:01'  FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;" -D /home/yashan/catalog
```
