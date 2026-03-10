When the database operates in [archiving mode](../Basic Database Management/Archive Management), the binary files generated after the redo log files are archived are referred to as archive log files. 

## Viewing Archive Log Files

### Viewing Physical Files

Log in to the database server as the installation user and navigate to the storage path of the archive log files to view the corresponding physical files. By default, archive log files are stored in the $YASDB_DATA/archive directory and support custom [configuration of the archiving path](../Basic Database Management/Archive Management.html#archive_local_dest).

```shell
$ cd $YASDB_DATA/archive
$ ls -lrt
total 15456
-rw-r----- 1 yashan yashan 12128256 Jun 17 11:45 arch_0_1.ARC
-rw-r----- 1 yashan yashan  3698688 Jun 18 17:14 arch_0_2.ARC
```

The default filename format for archive log files is `arch_{resetid}_{sequence}.ARC`, which in YAC Deployment may also be `arch{instanceId}_{resetid}_{sequence}.ARC`. The specific field meanings are as follows:

- resetid: The reset id of the redo log; this value increments by 1 every time the redo timeline is reset (e.g., when failover is executed, or the database is opened in RESETLOGS mode).

- sequence: The sequence number of the archive log files.

- instanceId: A unique field in YAC Deployment used to mark the instance ID to which the archive log files belong. The archive log files for instance 1 will omit the instanceId field.

### Viewing through Views

You can view information about the archive log files, such as the sequence number, filename, and archive time, through the [V$ARCHIVED_LOG](../../Reference Manual/System Views/Dynamic Performance Views/V$ARCHIVED_LOG) view.

```sql
SELECT name,SEQUENCE#,completion_time,backup_count FROM V$ARCHIVED_LOG;

NAME                                       SEQUENCE#    COMPLETION_TIME   BACKUP_COUNT
------------------------------------------ ------------ ----------------- ------------
$YASDB_DATA/archive/arch_0_1.ARC              1 2022-06-17                           2
$YASDB_DATA/archive/arch_0_2.ARC              2 2022-06-18                           1
```

## Backup and Recovery of Archive Log Files

If the archive log files are continuous and complete (or have been properly backed up), you can carry out a Point-in-Time Recovery (PITR). This means that you can first restore the database to the backup time point using the database backup set and then apply these contiguous and complete archive log files (or restore the archive log files first and then apply), allowing the database to be restored to any moment from the time point of the backup set to the latest existing archive log files.

Therefore, it is recommended to regularly back up the archive log files and configure the cleanup policy to only allow deletion after backups have reached a specified count (ARCHIVELOG_DELETION_POLICY = 'BACKED UP integer TIMES'), thereby enabling the database to restore as much data as possible during recovery, minimizing data loss.

In Standalone Deployment and YAC Deployment, you can use the BACKUP ARCHIVELOG statement and RESTORE ARCHIVELOG statement to back up and recover the archive log files individually. In ISC Distributed Cluster Deployment, archive log files are backed up by backing up the entire database.

For specific backup and recovery operation steps, please refer to [Backup and Recovery](.././备份恢复/00备份恢复).

<span id="cleanup_manual" name="cleanup_manual" class="yaslink"></span>

## Manual Cleanup of Archive Log Files

YashanDB provides an [automatic cleanup mechanism](../Basic Database Management/Archive Management.html#auto_cleanup), which typically requires no human intervention as long as parameter configuration meets business needs. However, if more disk space is needed for occasional scenarios, database administrators can manually clean up archive log files, and manual cleanup will not interfere with the automatic cleanup mechanism.

Manual cleanup of archive log files refers to cleaning them up by executing SQL statements. **Do not directly delete the corresponding files in the operating system path**. During cleanup, you can specify the scope of archive log files based on the following dimensions (actual cleanup still follows the cleanup principles and policies). The corresponding range basis can be obtained by querying the [V$ARCHIVED_LOG](../../Reference Manual/System Views/Dynamic Performance Views/V$ARCHIVED_LOG) view:

|Dimension |Description |Scope Basis |
|--------------------|------------------------|--------------|
| ALL                | Clean all archive log files that meet the policy. | - |
| SEQUENCE integer   | Clean all archive log files meeting the policy before the specified sequence number. | SEQUENCE# field of V$ARCHIVED_LOG view |
| TIME date          | Clean the archive log files generated before the specified time that meet the cleanup conditions. | NEXT_TIME field of V$ARCHIVED_LOG view |
| SCN integer        | Clean the archive log files that meet the cleanup conditions before the specified SCN. | NEXT_CHANGE# field of V$ARCHIVED_LOG view |

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Check the configuration of ARCHIVELOG_DELETION_POLICY and ARCH_CLEAN_IGNORE_MODE parameters.

    ```sql
    -- Check ARCHIVELOG_DELETION_POLICY
    SHOW PARAMETER ARCHIVELOG_DELETION_POLICY

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCHIVELOG_DELETION_POLICY                                       SHIPPED TO ALL STANDBY, BACKED UP 2 TIMES

    -- If ARCHIVELOG_DELETION_POLICY = DEFAULT, custom policy will follow the value of ARCH_CLEAN_IGNORE_MODE parameter
    SHOW PARAMETER ARCH_CLEAN_IGNORE_MODE

    name                                                             value
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCH_CLEAN_IGNORE_MODE                                           BACKUP
    ```

3. If the policy-related configurations do not meet the requirements, modify the corresponding parameter values before executing subsequent operations.

4. To avoid accidental deletion of archive log files, it is recommended to back them up first.

    ::: tabs

    == Standalone Deployment, YAC Deployment 

    In Standalone Deployment and YAC Deployment, you can back up archive log files individually. For specific operation steps, please refer to [Backup archive log files](../Backup and Recovery/Backup and Recovery Using SQL Statements/Backup.html#backup_arch).

```sql
BACKUP ARCHIVELOG ALL FORMAT '/data/backup/all_20211209191000' TAG 'yas_allarchive_backup' PARALLELISM 3;
```

    == ISC Distributed Cluster Deployment 

    In ISC Distributed Cluster Deployment, archive log files can only be backed up by backing up the entire database. For specific operation steps, please refer to [Backup entire database](../Backup and Recovery/Backup and Recovery Using yasrman/Backup).

```shell
$ yasrman sys/********@192.168.1.2:1688 
-c "BACKUP CLUSTER TAG 'full_backup' FULL FORMAT 'full_001' PARALLELISM 3" 
-D /home/yashan/catalog
```
:::

5. According to the reference dimensions for cleaning up archive log files, check the corresponding dimension range reference values.

    ```sql
    SELECT NAME,SEQUENCE#,NEXT_CHANGE#,NEXT_TIME,BACKUP_COUNT FROM V$ARCHIVED_LOG;

    NAME                                SEQUENCE#    NEXT_CHANGE#        NEXT_TIME                 BACKUP_COUNT
    ----------------------------------- ----------- ------------------ --------------------------- ------------
    $YASDB_DATA/archive/arch_0_1.ARC    1    707838135675723776 2025-06-23 11:20:44.842706         2
    $YASDB_DATA/archive/arch_0_2.ARC    2    708408122494455808 2025-06-25 02:00:01.780873         0
    $YASDB_DATA/archive/arch_0_3.ARC    3    708557045713440768 2025-06-25 12:05:59.988633         0
    ```

6. Manually clean up archive log files; for specific syntax, please refer to [ALTER DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE.html#deletearchivelogclauses).

    ```sql
    -- Method 1: Based on SCN to specify the range, delete archive log files with SCN less than or equal to 708408122494455808 that meet the cleanup policy
    ALTER DATABASE DELETE ARCHIVELOG UNTIL SCN 708408122494455808;

    -- Method 2: Based on time to specify the range, delete archive log files generated on or before 2025-01-06 11:30:00 that meet the cleanup policy
    ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME TO_DATE('2025-01-06 11:30:00', 'yyyy-mm-dd hh24:mi:ss');

    -- Method 3: Based on archive sequence number to specify the range, delete archive log files with sequence number less than or equal to 2 that meet the cleanup policy
    ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 2;
    
    -- Method 4: Delete all archive log files that can be cleaned
    ALTER DATABASE DELETE ARCHIVELOG ALL;
    ```
