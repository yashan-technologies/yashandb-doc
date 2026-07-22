Before executing a backup operation, you can first plan the following [common attributes](../../../Tools Guide/yasrman/User Guide for yasrman/BACKUP.md#backupCommonSpecifier), and then select the appropriate operation based on the database objects to be backed up.

|Attribute  |Configuration Description |Default Configuration |
|--------------------|--------------|-------|
| Compression | Specifies compression settings via the `COMPRESSION` keyword, including compression algorithm and compression level:<br />* Compression algorithm: specified via the `ALGORITHM` keyword, supporting ZSTD and LZ4.<br />* Compression level: supports LOW, MEDIUM, and HIGH. | \* No compression by default.<br />* If `COMPRESSION` is specified but its sub-attributes are omitted, the default is ZSTD algorithm and LOW level. |
| Encryption | Specifies encryption settings via the `ENCRYPTION` keyword, including encryption algorithm and key:<br />* Encryption algorithm: supports AES128, AES192, AES256, and SM4.<br />* Key: specified via the `IDENTIFIED BY` keyword; key constraints follow the same rules as [database user password rules](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER.md#password). | \* No encryption by default.<br />* If `ENCRYPTION` is specified but the encryption algorithm is not explicitly specified, the default is SM4 algorithm. |
| Parallelism | Specifies parallelism via the `PARALLELISM` keyword; valid range is [1,16]. | 2 |
| Backup Destination | Specifies the backup destination via the `DEST` keyword:<br />* **DEST SERVER**: Indicates backing up to the database server side — that is, the backup set is saved on the server where the database resides (in YAC/distributed cluster deployments, the `FORMAT` can be used to specify the YFS path to store the backup set files on shared storage).<br />* **DEST CLIENT**: Indicates backing up to the client side — that is, the backup set is saved on the server where the yasrman tool resides. | SERVER |
| Backup set file name | Specifies the backup set file name (can include a path).<br />In YAC/distributed cluster deployments, you can also specify a [YFS ](../../../Database Administration/Storage Management/YFS Management/00YFS Management)path to store the backup set on shared storage. | Depends on the backup destination:<br />* **DEST SERVER**: `$YASDB_DATA/backup/bak_{date}`<br />* **DEST CLIENT**: `catalog/backup/bak_{date}` |
| File Shard Specification | Specifies the file shard specification via the `SECTION SIZE` keyword. Files larger than this value will be split into multiple smaller files for backup. Valid range is [128M, 32T].<br />If the specified value is not an integer multiple of 1M, it will be rounded down to the nearest 1M alignment. | Automatically calculated by the system |

## Backup the Entire CDB

### Database Backup



Database backup will back up its control files, data files, redo log files, and partial archive log files.

#### Operational Instructions

- Only the SYS superuser or common users) with SYSDBA or SYSBACKUP privilege can perform backup operations.

- Database backups cannot be performed concurrently with operations like adding, deleting, or resizing data files, such as tablespaces and redo logs. When performing backup operations on a standby database, it will also block the application of redo logs related to tablespaces and data files on the standby database, which will lead to an increased log gap between primary and standby databases. The standby database will resume normal redo application once its backup operation is complete.

- If a full backup is executed while the database is in read-only mode or in an abnormal state, the FORCE keyword can be specified, but forced backups will not be recorded in the system tables.

- Database environment preparation before backup:

    - The CDB root and all PDBs must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the backup file storage path, you must ensure that the disk space at that path is sufficient. If backup set files already exist at the target path, when backing up again, it is recommended to specify a different filename to avoid overwriting existing files.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

 



#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates that a full backup is being executed.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_backup' FULL FORMAT 'CDB_full_001' COMPRESSION ALGORITHM ZSTD LOW PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### Incremental Backup

An incremental backup refers to the initial execution of a baseline backup (LEVEL 0), followed by subsequent backups that only capture changes (LEVEL 1). When backing up LEVEL 1, it can be based on LEVEL 0 (i.e., cumulative incremental backup) or based on the preceding LEVEL 1 (i.e., differential incremental backup).

Specifying the INCREMENTAL keyword indicates that an incremental backup is being executed.

Incremental backups must adhere to the following usage rules:

- In the same incremental backup chain, the number of consecutive LEVEL 1 incremental backups must not exceed 1000.

- Multiple backup sets in the same incremental backup chain are allowed to employ different compression strategies.

- Multiple backup sets in the same incremental backup chain must use consistent encryption strategies (either all encrypted or all unencrypted, and if encrypted, the same password), but different encryption algorithms may be used.

- Backup sets in the same incremental backup chain must be placed on the same device.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'CDB_incr_0_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

#LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'CDB_incr_1_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup range only supports specification via `ALL`, `UNTIL TIME`, or `UNTIL SCN`.

 

#### Operational Instructions

- Only the SYS superuser or common users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- When backing up archive log files, the backup range can be specified down to a specific log, but the actual backup will be performed at the file level, meaning that the entire file containing the target log will be backed up.

- Database environment preparation before backup:

    - The CDB root and all PDBs must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

#### Operational Example

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP ARCHIVELOG ALL FORMAT 'CDB_Arch_01' TAG 'CDB_Arch' COMPRESSION" \
-D /home/yashan/catalog
```

## Backup the CDB Root

### Database Backup



Database backup will back up its control files, data files, redo log files, and partial archive log files.

#### Operational Instructions

- Only the SYS superuser or common users) with SYSDBA or SYSBACKUP privilege can perform backup operations.

- Database backups cannot be performed concurrently with operations like adding, deleting, or resizing data files, such as tablespaces and redo logs. When performing backup operations on a standby database, it will also block the application of redo logs related to tablespaces and data files on the standby database, which will lead to an increased log gap between primary and standby databases. The standby database will resume normal redo application once its backup operation is complete.

- If a full backup is executed while the database is in read-only mode or in an abnormal state, the FORCE keyword can be specified, but forced backups will not be recorded in the system tables.

- Database environment preparation before backup:

    - The CDB root must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the backup file storage path, you must ensure that the disk space at that path is sufficient. If backup set files already exist at the target path, when backing up again, it is recommended to specify a different filename to avoid overwriting existing files.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

 



#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates that a full backup is being executed.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_backup' FULL FORMAT 'root_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### Incremental Backup

An incremental backup refers to the initial execution of a baseline backup (LEVEL 0), followed by subsequent backups that only capture changes (LEVEL 1). When backing up LEVEL 1, it can be based on LEVEL 0 (i.e., cumulative incremental backup) or based on the preceding LEVEL 1 (i.e., differential incremental backup).

Specifying the INCREMENTAL keyword indicates that an incremental backup is being executed.

Incremental backups must adhere to the following usage rules:

- In the same incremental backup chain, the number of consecutive LEVEL 1 incremental backups must not exceed 1000.

- Multiple backup sets in the same incremental backup chain are allowed to employ different compression strategies.

- Multiple backup sets in the same incremental backup chain must use consistent encryption strategies (either all encrypted or all unencrypted, and if encrypted, the same password), but different encryption algorithms may be used.

- Backup sets in the same incremental backup chain must be placed on the same device.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.




***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'root_incr_0_002' COMPRESSION ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

# LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'root_incr_1_002' COMPRESSION ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup range only supports specification via `ALL`, `UNTIL TIME`, or `UNTIL SCN`.

 

#### Operational Instructions

- Only the SYS superuser or common users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- When backing up archive log files, the backup range can be specified down to a specific log, but the actual backup will be performed at the file level, meaning that the entire file containing the target log will be backed up.

- Database environment preparation before backup:

    - The the CDB root must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

#### Operational Example

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP ARCHIVELOG ROOT UNTIL TIME '2026-01-09 14:20:54.336905' FORMAT 'root_Arch_01' TAG 'root_Arch' COMPRESSION;" \
-D /home/yashan/catalog
```

## Backup Specified PDB(s)

### Database Backup



Database backup will back up its control files, data files, redo log files, and partial archive log files.

#### Operational Instructions

- Only the SYS superuser or common users) with SYSDBA or SYSBACKUP privilege can perform backup operations.

- Database backups cannot be performed concurrently with operations like adding, deleting, or resizing data files, such as tablespaces and redo logs. When performing backup operations on a standby database, it will also block the application of redo logs related to tablespaces and data files on the standby database, which will lead to an increased log gap between primary and standby databases. The standby database will resume normal redo application once its backup operation is complete.

- If a full backup is executed while the database is in read-only mode or in an abnormal state, the FORCE keyword can be specified, but forced backups will not be recorded in the system tables.

- Database environment preparation before backup:

    - The CDB root and specified PDB(s) must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the backup file storage path, you must ensure that the disk space at that path is sufficient. If backup set files already exist at the target path, when backing up again, it is recommended to specify a different filename to avoid overwriting existing files.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

 



#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates that a full backup is being executed.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_backup' FULL FORMAT 'pdb1_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### Incremental Backup

An incremental backup refers to the initial execution of a baseline backup (LEVEL 0), followed by subsequent backups that only capture changes (LEVEL 1). When backing up LEVEL 1, it can be based on LEVEL 0 (i.e., cumulative incremental backup) or based on the preceding LEVEL 1 (i.e., differential incremental backup).

Specifying the INCREMENTAL keyword indicates that an incremental backup is being executed.

Incremental backups must adhere to the following usage rules:

- In the same incremental backup chain, the number of consecutive LEVEL 1 incremental backups must not exceed 1000.

- Multiple backup sets in the same incremental backup chain are allowed to employ different compression strategies.

- Multiple backup sets in the same incremental backup chain must use consistent encryption strategies (either all encrypted or all unencrypted, and if encrypted, the same password), but different encryption algorithms may be used.

- Backup sets in the same incremental backup chain must be placed on the same device.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'pdb1_incr_0_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

# LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'pdb1_incr_1_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup range only supports specification via `ALL`, `UNTIL TIME`, or `UNTIL SCN`.

 

#### Operational Instructions

- Only the SYS superuser or common users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- When backing up archive log files, the backup range can be specified down to a specific log, but the actual backup will be performed at the file level, meaning that the entire file containing the target log will be backed up.

- Database environment preparation before backup:

    - The CDB root and specified PDB(s) must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../Tools Guide/yasrman/User Guide for yasrman/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

#### Operational Example

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE ARCHIVELOG pdb1 ALL FORMAT 'pdb1_Arch_01' TAG 'pdb1_Arch' COMPRESSION;" \
-D /home/yashan/catalog
```
