## General Description

RESTORE ARCHIVELOG is used to restore the specified range of archive log files from a backup to the target location.

Restoration can only be performed in MOUNT mode after the database has completed the RESTORE operation and has not executed RECOVER. Furthermore, the version of the database must be completely consistent with the version of the database that generated the backup set. Version checks include verifying that the DBID and RESTORE TIME of both the archive files and the database are consistent. The RESTORE TIME of the archive backup set can be queried from the DBA_BACKUP_SET view or using the yasrman list command. The RESTORE TIME of the target database to be restored can be checked using the V$DATABASE view.

This statement is not applicable for ISC Distributed Cluster Deployment.

For detailed instructions on backup and recovery operations, please refer to [Backup and Recovery](../../All Manuals/数据库管理/备份与恢复/00备份与恢复).

## Statement Definition

**restore archivelog::=**

```ebnf+diagram
syntax::= RESTORE archivelog archivelogRangeSpecifier [DECRYPTION  password] [PARALLELISM integer] FROM (SEARCHDIR|BACKUPSET) "archdirpath" [TO "destpath"]
```

**archivelog_range_specifier::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN interger AND | UNTIL SCN ) interger | 
(FROM SEQUENCE | SEQUENCE BETWEEN interger AND | UNTIL SEQUENCE ) interger [THREAD interger] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL))
```
<span id="archivelograngespecifier" name="archivelograngespecifier" class="yaslink"></span>

### 1. archivelogRangeSpecifier

Used to specify the range of archive log files to be restored, following the same method as [BACKUP ARCHIVELOG](BACKUP ARCHIVELOG).

If any specified archive log files do not exist within the range (for example, if files have been deleted or if the archive log files corresponding to a certain sequence number have not yet been generated), the restoration operation will fail and return error code YAS-02540.

#### 1.1. FROM

In the archive restoration statement, used with SCN, SEQUENCE, or TIME to specify the starting point for restoring archive log files.

When specifying the FROM directive to restore archive log files, the endpoint for the restoration is the last archive file before the current database's log recovery point, which can be found in the ASN of the RCY_POINT field in the V$DATABASE view.

#### 1.2. BETWEEN … AND …

In the archive restoration statement, used with SCN, SEQUENCE, or TIME to specify the backup range of archive log files.

When specifying the BETWEEN … (start point) AND … (end point) directive to restore archives, both the start and end points for the restoration are the specified values. The range should be a subset of [current database's log flush point ASN, last archive file before the current database's log recovery point].

#### 1.3. UNTIL

In the archive restoration statement, used with SCN, SEQUENCE, or TIME to specify the endpoint for restoring archive log files.

When specifying the UNTIL directive to restore archive log files, the starting point for restoration is the current database's log flush point ASN, which can be found in the ASN of the FLUSH_POINT field in the V$DATABASE view.

### 2. DECRYPTION

This statement is used to specify decryption during the restoration of a backup set, and the decryption password must be specified at the same time.

### 3. PARALLELISM

This statement is used to specify the level of multi-threaded restoration, with a range of \[1,8\]. If omitted, the default is 2.

### 4. FROM SEARCHDIR/BACKUPSET

This statement specifies the set of archive backup sets. If there are multiple sets of archive backup sets in the directory, the SEARCHDIR keyword can be used to specify the folder of the sets (the parent directory of the backup set directory). If a single backup set is specified, use the BACKUPSET keyword.

> **Note**: 
>
> If the encryption passwords of different backup sets in the collection folder are inconsistent, the backup sets with inconsistent passwords will be automatically skipped during the restoration process.
>
> To restore archive backup sets with different decryption passwords separately, it is necessary to execute the restoration statement multiple times, each corresponding to the decryption key of the target backup set.

### 5. TO destpath

This statement specifies the target recovery path for the archive; be sure that this path exists. If omitted, the default will be the path configured by the ARCHIVE_LOCAL_DEST parameter.