The following recovery operations can be performed using the [yasrman](../../../Tools Guide/yasrman/00yasrman) tool:

- **Restore Entire Database**: This applies to all deployment forms, and the commands/usage for Standalone/YAC/Distributed Cluster Deployment are completely consistent. Based on specific operational details, it can be further divided into complete restore of the entire database and point-in-time restore of the entire database. The *yasrman* tool executes the RESTORE DATABASE command which by default includes RECOVER and OPEN database operations. After execution, the database is in an OPEN state.

- **Restore Tablespace**: This applies to Standalone Deployment. In a primary/standby high availability environment, operations can only be executed on the primary database. For more details, see [Tablespace Recovery](#rmanSpaceRestore).

- **Restore Archive Log Files**: This applies to both Standalone/YAC/Distributed Cluster Deployment, with completely consistent commands/usage. For more details, see [Archive Recovery](#rmanArchiveFileRestore).

|Operation |Operation Scenario |Backup Set |
|--------------------|--------------|-------|
| [Restore Entire Database](#restorealldb) | Use when a complete restore of the database is required in any deployment form | Full Database Backup Set<br/> For PITR (PITR), archive log files or their backup sets are also required |
| [Restore Tablespace](#rmanSpaceRestore)  | Use when a specified tablespace needs to be restored individually within Standalone Deployment | Tablespace Backup Set, archive log files required for tablespace RECOVER |
| [Restore Archive Log Files](#rmanArchiveFileRestore) | Use in the PITR process which involves restoring archives in Standalone/YAC/Distributed Cluster Deployment, automatically executed by *yasrman*, manual restoration of archive log files is not necessary | Archive log files backup set                           |

Executing recovery operations requires:

- The deployment status of the target database (number of nodes, node listening addresses, paths, etc.) must be identical to that of the source database of the backup set.

- The target database and the source database of the backup set must be compatible versions (i.e., the first three components of the version number must be identical).

When the amount of data to be restored is large or if desired efficiency is to be enhanced, the degree of concurrency can be appropriately increased, with a default concurrency of 2.

During the recovery process, detailed logs can be viewed in the recovery log file through `$YASDB_DATA/log/run/run.log`.

> **Caution**:
> 
> In a dual replication group primary/standby deployment environment, instances will all become primary databases after executing database restore operations. If the target instance is a standby database and the original primary database is alive, recovery operations must not be performed on that instance; otherwise, issues like dual primary may lead to deployment state or data anomalies. To rebuild a standby database, please perform the standby database [BUILD](../../../Development Guide/SQL Reference Manual/SQL Statements/BUILD DATABASE) operation.

<span id="restorealldb" name="restorealldb"></span>

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

- In Standalone One-primary/Multi-standby Deployment, before performing recovery operations on the primary database (especially when only an isolated primary database remains in fault scenarios), the automatic failover functionality must first be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to prevent unintentional failover due to undetected standby database heartbeats, and restore the corresponding configuration after completion of recovery as needed.

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- If slice files for the LSC table were generated after the backup set, the following scenarios during point-in-time recovery of the entire database might lead to data inconsistencies in the LSC table after recovery:

  - If all original slice files for the database exist but an older point in time is used to restore, extra slice files may appear in the LSC table.

  - If the original slice files of the database are cleared and PITR is used, the LSC table may lose data stored in the slice files.

- Database environment preparation before recovery:

  - The database instance must be in NOMOUNT stage.

  - Directories for data files, archive files, and bucket directories must be cleared on all nodes.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be [opened](../../../Product Security/Encryption/Storage Encryption/Key Management.md#openwallet) before performing the recovery operation.

### Complete Restore of Entire Database

Directly restore database based on the backup set of the full database backup.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore database from tag 'full_1'" \
-D /home/yashan/catalog
```

***Example*** for ISC Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore cluster from tag 'full_1'" \
-D /home/yashan/catalog
```

<span id="untilTimeRestoreDb" name="untilTimeRestoreDb"></span>



### Point-In-Time Restore of Entire Database

When recovering the entire database based on a point in time, the system first uses the backup set from the full database backup to restore data. If the target time point is not reached, the system automatically searches for eligible archive log files (or their backup sets) for replay until the target time point is reached or the latest log entry is replayed. If all archives are replayed but the target time point is still not reached, a message will be prompted: "the database cannot recover to the target time, and the current time of the database is {the actual nearest time point that can be recovered to}". In this case, ALTER DATABASE OPEN RESETLOGS must be executed to reset the redo log numbers when starting the database.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore database from tag 'full_1' until time '2023-11-13'" \
-D /home/yashan/catalog

# Specify SCN and database ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore database dbid 12573483 until time '2023-11-13 18:55:00'" \
-D /home/yashan/catalog

# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore database from tag 'full_1' until scn 499833430256541696" \
-D /home/yashan/catalog
```

<span id="rmanSpaceRestore" name="rmanSpaceRestore"></span>

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
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore tablespace spc_test from tag 'spc_test_bak' " \-D /home/yashan/catalog

 
-- 2. Execute RECOVER TABLESPACE
$ yasrman sys/********@192.168.1.2:1688 \
-c "recover tablespace spc_test maxsize 1280M " \
-D /home/yashan/catalog
```

<span id="rmanArchiveFileRestore" name="rmanArchiveFileRestore"></span>

## Archive Recovery

If directly using yasrman to execute point-in-time recovery of the entire database , there is no need to separately execute archive recovery. This operation may only be used as an intermediate step when performing point-in-time recovery using [SQL statements](#untilTimeRestoreDb).

Archive recovery refers to restoring files from the archive backup set to the database's archive directory and registering them in the database during the complete database recovery (RESTORE DATABASE) but before the log recovery (RECOVER DATABASE). This aims to supplement missing archive log files from the database backup set as much as possible to recover more data.

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
$ yasrman sys/********@192.168.1.2:1688 \
-c "restore archivelog all from tag 'arch_bak002'" \
-D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 \
-c "restore archivelog sequence between 10 and 20" \
-D /home/yashan/catalog
```
