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

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

 



#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates that a full backup is being executed.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_backup' FULL FORMAT 'CDB_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### Incremental Backup

An incremental backup refers to the initial execution of a baseline backup (LEVEL 0), followed by subsequent backups that only capture changes (LEVEL 1). When backing up LEVEL 1, it can be based on LEVEL 0 (i.e., cumulative incremental backup) or based on the preceding LEVEL 1 (i.e., differential incremental backup).

Specifying the INCREMENTAL keyword indicates that an incremental backup is being executed.

Incremental backups must adhere to the following usage rules:

- In the same incremental backup chain, the number of consecutive LEVEL 1 incremental backups must not exceed 1000.

- Multiple backup sets in the same incremental backup chain are allowed to employ different compression strategies.

- Multiple backup sets in the same incremental backup chain must use consistent encryption strategies (either all encrypted or all unencrypted, and if encrypted, the same password), but different encryption algorithms may be used.

- The DEST option must remain consistent within the same incremental backup chain.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.



##### LEVEL 0

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'CDB_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'CDB_incr_1_002'" \
-D /home/yashan/catalog
```



#### Compressed Backup

When performing database backups, you can specify the backup set compression strategy using the COMPRESSION keyword. The options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the COMPRESSION options are omitted, the default is ZSTD algorithm with LOW level.

Multiple backup sets in the same incremental backup chain are allowed to adopt different compression strategies.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### Encrypted Backup

When performing database backups, you can specify the backup set encryption strategy using the ENCRYPTION keyword. The options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; if omitted, SM4 is used by default.

- The IDENTIFIED BY keyword specifies the key.

Multiple backup sets in the same incremental backup chain must follow a consistent encryption strategy (either all encrypted or all unencrypted, and if encrypted, the same password); however, different encryption algorithms may be used.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup range specification and general backup attributes such as compression and encryption are consistent with those of SQL statement backups.

 

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
-c "BACKUP ARCHIVELOG TIME BETWEEN '2025-08-28 08:14:01' AND '2025-09-28 11:14:01'  FORMAT 'CDB_Arch_01' TAG 'CDB_Arch' COMPRESSION;" \
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

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

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

- The DEST option must remain consistent within the same incremental backup chain.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.



##### LEVEL 0

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'root_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'root_incr_1_002'" \
-D /home/yashan/catalog
```



#### Compressed Backup

When performing database backups, you can specify the backup set compression strategy using the COMPRESSION keyword. The options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the COMPRESSION options are omitted, the default is ZSTD algorithm with LOW level.

Multiple backup sets in the same incremental backup chain are allowed to adopt different compression strategies.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### Encrypted Backup

When performing database backups, you can specify the backup set encryption strategy using the ENCRYPTION keyword. The options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; if omitted, SM4 is used by default.

- The IDENTIFIED BY keyword specifies the key.

Multiple backup sets in the same incremental backup chain must follow a consistent encryption strategy (either all encrypted or all unencrypted, and if encrypted, the same password); however, different encryption algorithms may be used.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup range specification and general backup attributes such as compression and encryption are consistent with those of SQL statement backups.

 

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
-c "BACKUP ARCHIVELOG ROOT TIME BETWEEN '2025-08-28 08:14:01' AND '2025-09-28 11:14:01'  FORMAT 'root_Arch_01' TAG 'root_Arch' COMPRESSION;" \
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

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

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

- The DEST option must remain consistent within the same incremental backup chain.

- During recovery, the baseline backup set and then the incremental backup sets must be restored sequentially.



##### LEVEL 0

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'pdb1_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'pdb1_incr_1_002'" \
-D /home/yashan/catalog
```



#### Compressed Backup

When performing database backups, you can specify the backup set compression strategy using the COMPRESSION keyword. The options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the COMPRESSION options are omitted, the default is ZSTD algorithm with LOW level.

Multiple backup sets in the same incremental backup chain are allowed to adopt different compression strategies.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### Encrypted Backup

When performing database backups, you can specify the backup set encryption strategy using the ENCRYPTION keyword. The options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; if omitted, SM4 is used by default.

- The IDENTIFIED BY keyword specifies the key.

Multiple backup sets in the same incremental backup chain must follow a consistent encryption strategy (either all encrypted or all unencrypted, and if encrypted, the same password); however, different encryption algorithms may be used.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### Archive Log Backup

The backup scope only supports specification through ALL, UNTIL TIME, or UNTIL SCN, while general backup attributes such as compression and encryption are consistent with backup using SQL statements.

 

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
