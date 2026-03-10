General Description
----

RESTORE DATABASE is used to restore database files from a backup set, returning to the state at the time of the backup.

If database flashback was enabled at the time of backup, it will remain enabled after the RESTORE DATABASE command completes, but the relevant resource information and content will be reset.

Restoration can only be performed when the database is in NOMOUNT state and the old data file has been deleted, and the database version must exactly match the version of the database used to create the backup set.

After a successful restoration, the database changes to MOUNT state. At this point, the [RECOVER](RECOVER DATABASE) operation must still be executed to restore the system to a specified point in time based on the logs.

This statement is not applicable to ISC Distributed Cluster Deployment.

For detailed operational descriptions of backup and recovery, please refer to [Backup and Recovery](../../../数据库管理/备份与恢复/00备份与恢复).

> **Note**: 
>
> If there are encrypted objects in the backup set, the corresponding key wallet file must be opened before performing the recovery operation.

Statement Definition
----

**restore database::=**

```ebnf+diagram
syntax::= RESTORE DATABASE [INCREMENTAL [NOREDO]] [DECRYPTION  password] FROM backup_path [PARALLELISM integer]
```

### 1. INCREMENTAL [NOREDO]

This statement is used to specify an incremental RESTORE to recover the database. The NOREDO field can only be used after the INCREMENTAL field is specified; the NOREDO field allows skipping the recovery of redo and archive files during recovery, improving RESTORE efficiency.

### 2. DECRYPTION

This statement is used to specify decryption during the recovery of the backup set, and the decryption password must be specified simultaneously.

### 3. backup\_path

This statement is used to specify the name of the backup set used for recovery.

### 4. PARALLELISM

This statement is used to specify the level of parallelism for multi-threaded recovery, with a value range of [1,16]. The default is 2 if omitted.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
RESTORE DATABASE DECRYPTION 12345 FROM 'backup';
```
