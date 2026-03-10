The backup operations can be performed using the [yasrman](../../../Tools Guide/yasrman/00yasrman) tool as follows:

- Full Database Backup: Applicable to all deployment types; the commands and usage for Standalone/YAC/Distributed Cluster Deployment are identical.

- Tablespace Backup: Applicable to Standalone Deployment; in a primary/standby high availability environment, the operation can only be executed by connecting to the primary database.

- Backup Archive Log Files: Applicable to both Standalone/YAC/Distributed Cluster Deployment, with identical commands and usage.

Backups will consume certain system and database resources. Please choose the backup window according to the business load. When the amount of data to be backed up is large or if you wish to enhance backup efficiency, you may increase the concurrency, which defaults to 2.

When backing up with the *yasrman* tool, you can choose to store the backup set files on the server side (DEST SERVER) or the tool side (DEST CLIENT).

## Full Database Backup

### Operational Instructions

- Only the SYS superuser or users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- Database backups cannot be performed concurrently with operations like adding, deleting, or resizing data files, such as tablespaces and redo logs. When performing backup operations on a standby database, it will also block the application of redo logs related to tablespaces and data files on the standby database, which will lead to an increased log gap between primary and standby databases. The standby database will resume normal redo application once its backup operation is complete.

- If a full backup is executed while the database is in read-only mode or in an abnormal state, the FORCE keyword can be specified, but forced backups will not be recorded in the system tables.

- In an ISC Distributed Cluster Deployment, if any node experiences a backup exception during the backup process, the backups of all nodes will fail.

- Database environment preparation before backup:

    - The database instance must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

### Backup Method

#### Full Backup

A full backup refers to a complete copy of all data at a specific point in time, independent of previous backup sets. A full backup set can restore all data. Specifying the FULL keyword indicates that a full backup is being executed.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP DATABASE TAG 'full_backup' FULL FORMAT 'full_001' PARALLELISM 3 DEST SERVER" 
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'full_backup' FULL FORMAT 'full_001' PARALLELISM 3" 
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
$ yasrman sys/password@192.168.1.2:1688 
-c "BACKUP DATABASE TAG 'incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'incr_0_002'" 
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/password@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'incr_0_002'" 
-D /home/yashan/catalog
```

##### LEVEL 1

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP DATABASE TAG 'incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'incr_1_002'" 
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'incr_1_002'" 
-D /home/yashan/catalog
```

### Compressed Backup

When performing database backups, you can specify the backup set compression strategy using the COMPRESSION keyword. The options include:

- The ALGORITHM keyword specifies the compression algorithm: ZSTD or LZ4.

- Compression levels: HIGH, MEDIUM, or LOW.

- If the COMPRESSION options are omitted, the default is ZSTD algorithm with LOW level.

Multiple backup sets in the same incremental backup chain are allowed to adopt different compression strategies.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP DATABASE TAG 'full_compress' COMPRESSION ALGORITHM ZSTD LOW" 
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'full_compress' COMPRESSION ALGORITHM ZSTD LOW" 
-D /home/yashan/catalog
```

### Encrypted Backup

When performing database backups, you can specify the backup set encryption strategy using the ENCRYPTION keyword. The options include:

- Encryption algorithms: AES128, AES192, AES256, or SM4; if omitted, SM4 is used by default.

- The IDENTIFIED BY keyword specifies the key.

Multiple backup sets in the same incremental backup chain must follow a consistent encryption strategy (either all encrypted or all unencrypted, and if encrypted, the same password); however, different encryption algorithms may be used.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP DATABASE TAG 'full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" 
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" 
-D /home/yashan/catalog
```

## Tablespace Backup

Tablespace backup is applicable only to Standalone Deployment. In a primary/standby high availability environment, the operation can only be executed by connecting to the primary database.

During tablespace backup, corresponding archive files and BUCKET files will not be backed up.

General backup attributes like compression and encryption are consistent with full database backups.

### Operational Instructions

- Only the SYS superuser or users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- The target tablespace cannot be a temporary tablespace, UNDO tablespace, or a corrupted tablespace.

- Tablespace backups cannot be performed concurrently with operations like adding or deleting tablespace files or switching tablespaces offline/online.

- Database environment preparation before backup:

    - The database instance must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

    - If you need to back up encrypted objects, you must first manually back up the key wallet file.

### Operational Example

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user tag 'user_bak'" -D /home/yashan/catalog
```

## Backup Archive Log Files

Backing up archive log files is applicable to both Standalone/YAC/Distributed Cluster Deployment.

The backup range specification and general backup attributes such as compression and encryption are consistent with those of SQL statement backups.

### Operational Instructions

- Only the SYS superuser or users with SYSDBA or SYSBACKUP privilege can perform backup operations.

- When backing up archive log files, the backup range can be specified down to a specific log, but the actual backup will be performed at the file level, meaning that the entire file containing the target log will be backed up.

- Database environment preparation before backup:

    - The database instance must be in the OPEN state and archiving mode must be enabled.

    - The catalog path must be created; please refer to [CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG) for specific operations.

    - If you need to customize the storage path for backup files, ensure that the path is empty and that there is enough disk space.

### Operational Example

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "BACKUP ARCHIVELOG TIME BETWEEN '2023-10-28 08:14:01' AND '2023-11-28 11:14:01'  FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;" -D /home/yashan/catalog
```
