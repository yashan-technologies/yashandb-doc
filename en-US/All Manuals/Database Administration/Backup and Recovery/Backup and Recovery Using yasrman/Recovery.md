The following recovery operations can be performed using the [yasrman](../../../Tools Guide/yasrman/00yasrman) tool:

- **Restore Entire Database**: This applies to all deployment forms, and the commands/usage for Standalone/YAC/Distributed Cluster Deployment are completely consistent. Based on specific operational details, it can be further divided into complete restore of the entire database and point-in-time restore of the entire database. The *yasrman* tool executes the RESTORE DATABASE command which by default includes RECOVER and OPEN database operations. After execution, the database is in an OPEN state.

- **Restore Tablespace**: This applies to Standalone Deployment. In a primary/standby high availability environment, operations can only be executed on the primary database. For more details, see [Tablespace Recovery](#rmanSpaceRestore).

- **Restore Archive Log Files**: This applies to both Standalone/YAC/Distributed Cluster Deployment, with completely consistent commands/usage. For more details, see [Archive Recovery](#rmanArchiveFileRestore).

|Operation |Operation Scenario |Backup Set |
|--------------------|--------------|-------|
| [Restore Entire Database](#restorealldb) | Use when a complete restore of the database is required in any deployment form | Full Database Backup Set<br/> For PITR (PITR), archive log files or their backup sets are also required |
| [Restore Tablespace](#rmanSpaceRestore)  | Use when a specified tablespace needs to be restored individually within Standalone Deployment | Tablespace Backup Set, archive log files required for tablespace RECOVER |
| [Restore Archive Log Files](#rmanArchiveFileRestore) | Use in the PITR process which involves restoring archives in Standalone/YAC/Distributed Cluster Deployment, automatically executed by *yasrman*, manual restoration of archive log files is not necessary | Archive log files backup set                           |

The database version and deployment state (number of nodes, node listener address, path, etc.) where recovery operations are executed must be identical to that of the database that generated the backup set.

When the amount of data to be restored is large or if desired efficiency is to be enhanced, the degree of concurrency can be appropriately increased, with a default concurrency of 2.

During the recovery process, detailed logs can be viewed in the recovery log file through `$YASDB_DATA/log/run/run.log`.

> **Caution**:
> 
> In a dual replication group primary/standby deployment environment, instances will all become primary databases after executing database restore operations. If the target instance is a standby database and the original primary database is alive, recovery operations must not be performed on that instance; otherwise, issues like dual primary may lead to deployment state or data anomalies. To rebuild a standby database, please perform the standby database BUILD operation.

<span id="restorealldb" name="restorealldb" class="yaslink"></span>

## Restore Entire Database

Complete restore of the entire database refers to decompressing and decrypting backup files from the full database backup set to the database directory, then applying the archive log files within the backup set to restore the database to a consistent state, thereby completely restoring the database to the backup time.

The *yasrman* tool's restore database command is equivalent to the following SQL statements:

1. Execute the RESTORE DATABASE statement.
2. Execute the RECOVER DATABASE statement.
3. OPEN the database.

PITR (Point-In-Time Recovery): Refers to restoring the database files and archive log files based on the database backup set to a state at a specific point in time after the backup until the latest time.

The *yasrman* tool's restore database … until time … command is equivalent to the following SQL statements:

1. Execute the RESTORE DATABASE statement.
2. Execute the RESTORE ARCHIVELOG statement.
3. Execute the RECOVER DATABASE UNTIL statement.
4. Reset the redo timeline and OPEN the database.

### Operation Instructions

- Only the SYS user can perform the entire database recovery.

- If the database backup set uses encrypted backups, the decryption keyword and password must be specified during recovery.

- In a standalone one-primary/multi-standby deployment, before performing recovery operations on the primary database (especially when only an isolated primary database remains in fault scenarios), the automatic failover functionality must first be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to prevent unintentional failover due to undetected standby database heartbeats, and restore the corresponding configuration after completion of recovery as needed.

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- If slice files for the LSC table were generated after the backup set, the following scenarios during point-in-time recovery of the entire database might lead to data inconsistencies in the LSC table after recovery:

  - If all original slice files for the database exist but an older point in time is used to restore, extra slice files may appear in the LSC table.

  - If the original slice files of the database are cleared and PITR is used, the LSC table may lose data stored in the slice files.

- Database environment preparation before recovery:

  - The database instance must be in NOMOUNT stage.

  - Directories for data files, archive files, and bucket directories must be cleared on all nodes.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before performing the recovery operation.

### Complete Restore of Entire Database

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1'" -D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore cluster from tag 'full_1'" -D /home/yashan/catalog
```

<span id="untilTimeRestoreDb" name="untilTimeRestoreDb" class="yaslink"></span>

### Point-In-Time Restore of Entire Database

If there aren't enough archive log files or the specified point in time (or SCN) is too large, the specified point in time cannot be reached even after application is completed, which will cause an error in the database. You can check the current_scn field in the V$DATABASE view to check the actual apply progress. If there are available copies of archive log files, you can register the archives and re-execute the restore operation. If there are no extra archives, the current point in time is the only option; you must continue with the resetlogs operation.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1' until time '2023-11-13'" -D /home/yashan/catalog

# Specify SCN and database ID
$ yasrman sys/********@192.168.1.2:1688 -c "restore database dbid 12573483 until time '2023-11-13 18:55:00'" -D /home/yashan/catalog

# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1' until scn 499833430256541696" -D /home/yashan/catalog
```

<span id="rmanSpaceRestore" name="rmanSpaceRestore" class="yaslink"></span>

## Tablespace Recovery

Tablespace recovery refers to decompressing and decrypting backup files from the corresponding tablespace backup set to the database directory, then applying the archive log files within the backup set to restore the tablespace to a consistent state, thus completely restoring the tablespace to the backup time.

Tablespace recovery operations only apply to Standalone Deployment. In a primary/standby high availability environment, operations can only be executed on the primary database.

### Operation Instructions

- Tablespace recovery can only be performed by the SYS superuser or users with SYSDBA or SYSBACKUP privileges.

- If the database backup set uses encrypted backups, the decryption keyword and password must be specified during recovery.

- Database environment preparation before recovery:

  - The database must be started to the MOUNT or OPEN stage.
  
  - The target tablespace must be in OFFLINE state, and all data files under that tablespace must be in OFFLINE or RECOVER state.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before performing the recovery operation.

### Operation Steps

1. Execute the RESTORE TABLESPACE to restore the corresponding files based on the specified tablespace backup set.

2. Execute the RECOVER TABLESPACE to perform consistency recovery on the tablespace-related files.

***Example*** for Standalone Deployment

```shell
-- 1. Execute RESTORE TABLESPACE
$ yasrman sys/********@192.168.1.2:1688 -c "restore tablespace spc_test from tag 'spc_test_bak' " -D /home/yashan/catalog
 
-- 2. Execute RECOVER TABLESPACE
$ yasrman sys/********@192.168.1.2:1688 -c "recover tablespace spc_test maxsize 1280M " -D /home/yashan/catalog
```

<span id="rmanArchiveFileRestore" name="rmanArchiveFileRestore" class="yaslink"></span>

## Archive Recovery

Archive recovery refers to restoring files from the archive backup set to the database's archive directory and registering them in the database during the complete database recovery (RESTORE DATABASE) but before the log recovery (RECOVER DATABASE). This aims to supplement missing archive log files from the database backup set as much as possible to recover more data.

If it is necessary to RESTORE the database to the mount state, the *yasql* tool must be used to connect to the target database and specify the backup set to execute RESTORE. After completion, the database will be in MOUNT state, after which the *yasrman* tool can be used to RESTORE the required archive files as needed. If you expect to restore target data to a specified point in time, it is recommended to use the *yasrman* tool for a one-click RESTORE of the database without needing to perform archive recovery separately. Refer to the operation method in [Point-In-Time Restore of Entire Database](#untilTimeRestoreDb).

> **Note**: 
>
> When restoring archive files, it will verify whether the archive backup set and the database version are consistent. Version verification includes checking if the archive files and database DBID and RESTORE TIME are consistent. The RESTORE TIME of the archive backup set can be queried from the DBA_BACKUP_SET view or using the yasrman list command, while the RESTORE TIME of the target database can be checked in the V$DATABASE view. Archive backup sets with version inconsistencies will not be recoverable and will be automatically skipped during the scanning process.

In a YAC/Distributed Cluster Deployment, the order of archive generation across instances is not consistent, meaning that the ASN for archives at the same time across different instances may vary. When using archive recovery operations, it is advisable to specify SCN or TIME range to limit recovery to avoid missing logs.

The methods for specifying recovery ranges and general backup attributes such as compression and encryption are consistent with those in SQL statement backups.

### Operation Instructions

- In YAC/Distributed Cluster Deployment, archive recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- Archive log files can only be restored as a complete set or subset of the existing archive backup sets, and methods for specifying the recovery range (SCN, SEQUENCE, or TIME) are not constrained by the backup range specification method.

- If the archive backup set uses encrypted backups, the DECRYPTION keyword and decryption password must be specified during recovery.

- Database environment preparation before recovery:

  - The database must have completed RESTORE DATABASE and started to MOUNT stage, but RECOVER DATABASE has not been executed yet (RESTORE must be executed using the *yasql* command).

  - Ensure that the target path for restoring archive log files exists. If TO destpath is omitted, it defaults to the path configured in ARCHIVE_LOCAL_DEST parameter (which defaults to $YASDB_DATA/archive).

### Operational Examples

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore archivelog all" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "restore archivelog sequence between 10 and 20" -D /home/yashan/catalog
```
