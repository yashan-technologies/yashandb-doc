Data backup and recovery are important mechanisms to ensure that data can be restored after encountering disasters or unforeseen events.

Traditional backup methods are divided into offline backup (cold backup) and online backup (hot backup). Cold backup refers to taking a copy of the file system through OS commands after shutting down the database, while hot backup refers to online backup performed during normal database operation.

YashanDB supports online backup, allowing users to regularly back up data according to their business needs, enhancing the database system's ability to respond to failures. In case of issues, appropriate backup sets are available to restore data and prevent data loss.

## Backup and Recovery Process

The backup and recovery process is illustrated in the following diagram:

![](./image/bak_rec.png)

The backup functionality requires the database to run in OPEN state and have archiving mode enabled. When performing a backup, data is read from physical files in data block format and written to the backup file. During the reading of physical files, the corresponding data blocks are locked to prevent data writes during the backup.

The recovery functionality requires the database to run in NOMOUNT state. During execution, the files in the backup set are copied back to the database's files in data block units, and archive log files are applied to achieve consistent recovery of the database.

## Backup Set

A backup set is a collection of files generated after performing a backup operation on the database. When the backup medium is disk, the backup set exists in the form of a folder, and users can customize the name and storage path of the backup set folder.

During the process of generating the backup set, operations such as slicing, compression, and encryption may be performed, so the size and number of backup files may not equal those of the target files.

The backup set may include the following files:

- backup_profile file: Metadata information of the backup set.
- backup_filelist file: Verification information of all files in the current backup set.
- ctrl*.bak files: Backup of the database control files.
- data*.bak files: Backup of all data files of the database.
- arch*.bak files: Backup set of the database archive log files.
- redo*.bak files: Backup set of the database redo files, generally found within the backup set generated on standby database.
- bucket*.bak files: Backup set of slice files.

## Backup Strategy

|Backup Strategy Item&nbsp;&nbsp; |&nbsp;&nbsp; |Description |
|---------|-----------|---------------|
| Backup Granularity | Full Database Backup | A copy of all data files of the database (including control files, data files, archive files, etc.) |
|  | Archive Backup | Backup operation performed on currently existing archive files of the target database |
| Backup Destination | Local Backup | Backup set stored on the local disk of the executing instance server, or on the shared storage accessible to the executing instance |
|  | Stream Backup | Also known as remote backup, sending the backup set to a remote server for storage over a network |
| Backup Set Compression |   | When performing a backup, different compression algorithms can be specified as needed for compressing the newly generated backup set. Decompression must be specified when using this type of backup set for recovery |
| Backup Set Encryption |   | When performing a backup, different encryption algorithms can be specified as needed for encrypting the backup data set. Decryption is required when using this type of backup set for recovery |

## Backup Methods

Backup methods are divided into full backup and incremental backup.

- Full Backup: A complete copy of the target files is taken. The backup set generated from a full backup contains a complete data file and can be used for restoration on its own.
- Incremental Backup: The first backup is a full data backup to create a LEVEL 0 backup set, followed by backing up only the modified data pages since the last backup to create a LEVEL 1 backup set. Incremental backups can reduce the space occupied by backup sets, but incremental backup sets cannot be used for recovery on their own; the dependent baseline backup set must be restored first. Incremental backups are divided into differential incremental backups and cumulative incremental backups.
    
    - Differential Incremental Backup: Incremental backups are performed using the most recent incremental backup set (which can be LEVEL 0 or LEVEL 1) as the baseline. During recovery, all incremental backup sets need to be restored sequentially.

	- Cumulative Incremental Backup: The most recent LEVEL 0 backup set serves as the baseline. During recovery, only the baseline backup set and the current latest cumulative incremental backup set need to be restored sequentially.

## Recovery Modes


Depending on different scenario needs, recovery can be divided into full recovery or point-in-time recovery (PITR) based on different backup sets.

- Full Recovery: The database is restored to the point in time when the backup was performed based on the corresponding backup set. For the full backup set, it can be restored directly at once; for incremental backup sets, the baseline backup set must be restored first, followed by the relevant incremental backup sets.
- Point-in-Time Recovery: Allows the database to be restored to any time between the backup time point and the latest time point.

## Backup and Recovery Operations

Backup and recovery operations support the use of SQL statements and the *yasrman* tool:

- Using SQL statements: The backup set must be stored on the database server.
    
    - Backup: [BACKUP DATABASE](../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE) statement		
	- Restore: [RESTORE DATABASE](../../开发手册/SQL参考手册/SQL语句/RESTORE DATABASE) and [RECOVER DATABASE](../../开发手册/SQL参考手册/SQL语句/RECOVER DATABASE) statements

- Using [yasrman](../../工具手册/yasrman/00yasrman) tool: Remote backup/recovery operations can be initiated, and the backup set can be optionally stored on the tool side or the database server side.

For detailed operational procedures regarding backup and recovery, please refer to [Backup and Recovery](../../数据库管理/备份恢复/00备份恢复).
In addition to directly performing hot backups and recovery, using the [exp](../../工具手册/exp/00exp) tool to export data forms file backups, and then using the [imp](../../工具手册/imp/00imp) tool to import data when needed can also achieve the effect of backup and recovery.
