The basic configuration for database flashback includes enabling/disabling the feature and managing flashback log files.

The full database flashback feature is unavailable in ISC Distributed Cluster Deployment.

##  Enabling Database Flashback

Database flashback is disabled by default and can be enabled based on operational requirements.

In primary-standby HA deployments, flashback configurations are independent between primary and standby databases. Typical flashback recovery refers to operations executed on the primary. Standby flashback is only applicable when the standby has handled transactions independently after failover and needs to revert to a pre-failover state to re-sync with the primary.

> **Note**:
>
> - Enabling database flashback may impact database write performance (estimated to reduce by approximately 8%).
>
> - After enabling database flashback, flashback logs will be automatically generated and will increase continuously during database operation, requiring disk space allocation.
>
> - When a standby database has database flashback enabled, all ARCHIVE_DEST_n, REQUIRED_SYNC_STANDBYS, and REPLICATION_ADDR parameters must be cleared before performing a failover.

###  Prerequisites

- The database must be in [archivelog mode](../../../实例管理/归档管理).

- The instance must be in MOUNT or OPEN state.

- In YAC/Distributed Cluster Deployment, flashback operations must be executed on the master instance (`INSTANCE_ROLE = 'MASTER_ROLE'` in [GV$INSTANCE](../../../../参考手册/系统视图/动态视图/GV$INSTANCE)).

- The user must have SYSDBA privileges or FLASHBACK-related permissions. 

###  Operation Steps

1. Connect and log in to the database with a user that meets the required permissions.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. The FLASHBACK_ON column in the [V$DATABASE](../../../../参考手册/系统视图/动态视图/V$DATABASE) view indicates whether the global flashback feature is enabled.

    ```sql
    SELECT FLASHBACK_ON FROM V$DATABASE;

    FLASHBACK_ON
    -----------------
    NO
    ```
    - FLASHBACK_ON = NO indicates flashback is disabled. Proceed to enable it as needed.

    - FLASHBACK_ON = YES indicates flashback is enabled.
    

3. Execute the following statement to enable database flashback: 

    ```sql
    ALTER DATABASE FLASHBACK ON;
    ```

##  Viewing Database Flashback Information

- The FLASHBACK_ON column in the [V$DATABASE](../../../../参考手册/系统视图/动态视图/V$DATABASE) view indicates whether the global flashback feature is enabled.

    ```sql
    SELECT FLASHBACK_ON FROM V$DATABASE;

    FLASHBACK_ON
    -----------------
    NO
    ```
    - FLASHBACK_ON = NO indicates flashback is disabled. Proceed to enable it as needed.

    - FLASHBACK_ON = YES indicates flashback is enabled.
    

- Use the [GV$FLASHBACK_DATABASE_LOG](../../../../参考手册/系统视图/动态视图/GV$FLASHBACK_DATABASE_LOG) or [V$FLASHBACK_DATABASE_LOG](../../../../参考手册/系统视图/动态视图/V$FLASHBACK_DATABASE_LOG) views to retrieve database flashback details, such as the oldest recoverable SCN and the storage space occupied by flashback log files.

    ```sql
    SELECT * FROM V$FLASHBACK_DATABASE_LOG;

    OLDEST_FLASHBACK_SCN OLDEST_FLASHBACK_TIME                                                 RETENTION_TARGET       TOTAL_FILE_SIZE
    --------------------- ---------------------------------------------------------------- --------------------- ---------------------
    723859443785293824 2025-08-07 17:51:37.017894                                                        1440            1224210944
    ```

## Managing Flashback log Files

Flashback log files are automatically generated when database flashback is enabled.

### Viewing Flashback Log Files

#### Viewing Physical Files

::: tabs
== Standalone Deployment

The default storage path for flashback logs is `$YASDB_DATA/fra`.

Log in to the database server with the installation user and navigate to this path to view physical files.

```shell
$ cd $YASDB_DATA/fra
$ ls -lrt
total 1457608
-rw-r----- 1 yashan yashan 1074827264 Aug 11 02:00 flb_file_0
-rw-r----- 1 yashan yashan  140962304 Aug 11 16:06 flb_file_1
-rw-r----- 1 yashan yashan    8421376 Aug 11 16:06 flb_ctrl
```
== YAC/Distributed Cluster Deployment

The default storage path for flashback logs is `+DG0/fra`.

Log in to the database server with the installation user and use the `yfscmd` command to manage/view these files.

```shell
$ yfscmd -p

YFSCMD [+] >  cd +DG0
YFSCMD [+DG0/fra] >  ls -l
Type Time                 Size     Space    Name
FILE 2025-08-12 09:47:13  262.03MB 263.00MB flb_ctrl
FILE 2025-08-12 09:47:15  1.00GB   1.00GB   flb_file_0_0
FILE 2025-08-12 09:47:17  1.00GB   1.00GB   flb_file_1_0
```
:::

Flashback log files consist of:

- flb_ctrl: Flashback control file.

- flb_file_{sequence}: Flashback log files, where {sequence} is an incrementing file serial number starting from 0.

#### Viewing through Views

::: tabs
== Standalone Deployment

The [V$FLASHBACK_DATABASE_LOGFILE](../../../../参考手册/系统视图/动态视图/V$FLASHBACK_DATABASE_LOGFILE) view provides information about flashback log files, including filenames, file sizes, and the SCN when files were created.

```sql
SELECT * FROM V$FLASHBACK_DATABASE_LOGFILE;

FILE_NAME                                        FILE_NUM THREAD#    SEQUENCE#             FILE_SIZE         FIRST_CHANGE# FIRST_TIME
-------------------------------------------- ------------ ------- ------------ --------------------- --------------------- -----------------------------
$YASDB_DATA/fra/flb_file_0                             0       0            0            1074827264    723743061460926464 2025-08-07 17:58:03.364484
$YASDB_DATA/fra/flb_file_1                             1       0            1             141250048    724923208865271808 2025-08-11 02:00:05.289373
```

== YAC/Distributed Cluster Deployment

The [GV$FLASHBACK_DATABASE_LOGFILE](../../../../参考手册/系统视图/动态视图/GV$FLASHBACK_DATABASE_LOGFILE)/[V$FLASHBACK_DATABASE_LOGFILE](../../../../参考手册/系统视图/动态视图/V$FLASHBACK_DATABASE_LOGFILE) views provide information about flashback log files, including filenames, file sizes, and the SCN when files were created.

```sql
SELECT INST_ID,FILE_NAME,FILE_NUM,THREAD#,SEQUENCE#,FILE_SIZE,FIRST_CHANGE#,FIRST_TIME FROM GV$FLASHBACK_DATABASE_LOGFILE;

    INST_ID FILE_NAME                           FILE_NUM THREAD#    SEQUENCE#             FILE_SIZE         FIRST_CHANGE# FIRST_TIME
----------- ------------------------------- ------------ ------- ------------ --------------------- --------------------- --------------------------------
          2 +DG0/fra/flb_file_1_0                      0       1            0                 59392    725391921513275392 2025-08-12 09:47:17.088202
          1 +DG0/fra/flb_file_0_0                      0       0            0                 67584    725391913422884864 2025-08-12 09:47:15.113009
```
:::


<span id="auto_cleanup" name="auto_cleanup" class="yaslink"></span>

###  Managing Flashback Log Auto-Purge Policies

YashanDB provides automatic flashback log purge policies, including cleanup rules and trigger conditions for auto-purge tasks.

If the default auto-purge policies do not meet requirements, **only modify parameter values—do not manually delete physical files**.

When automatically cleaning up flashback logs, only files earlier than [permanent restore points](./管理还原点) are removed.

#### Cleanup Policy

The purge policy depends on the following parameters: 

|Parameter Name |Parameter Description |Default Value |
|--------------------|-------------------------------|-------|
| DB_FLASHBACK_FILE_DEST_SIZE  | Maximum allowed disk space for flashback log files (in bytes). <br/> It also serves as the trigger condition for the flashback log file cleanup task — after the cleanup task is triggered, all flashback log files that are older than the permanent restore point and also meet the maximum retention period (controlled by the DB_FLASHBACK_RETENTION_TARGET parameter) requirement will be deleted. | 8G |
| DB_FLASHBACK_RETENTION_TARGET  | Maximum retention period for flashback log files (in minutes).<br/>It also serves as the cleanup policy for flashback log files (i.e., whether they are eligible for cleanup). | 1440 |

>**Caution**：
>
> If the automatic cleanup task cannot ensure that the total capacity of all existing flashback log files is ≤ the value of the DB_FLASHBACK_FILE_DEST_SIZE parameter — for example, if no flashback log files meet the cleanup criteria and thus cannot be automatically cleaned — the business may become blocked. The system will print an operational log indicating insufficient space, requiring manual intervention to either increase the value of the DB_FLASHBACK_FILE_DEST_SIZE parameter or manually delete unnecessary [permanent restore points](./管理还原点).

To view purge policy parameters: 

```sql
SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'DB_FLASHBACK_%';

NAME                                                             VALUE                                        
---------------------------------------------------------------- ----------------------------------------------------------------
DB_FLASHBACK_RETENTION_TARGET                                    1440                                         
DB_FLASHBACK_FILE_DEST_SIZE                                      8G
```

#### Adjust Automatic Cleanup Strategy

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


3. Adjust parameter values as needed and specify the scope of effectiveness.

    ```sql
    -- Set the triggering condition for automatic cleanup of flashback log files to 16G
    ALTER SYSTEM SET DB_FLASHBACK_FILE_DEST_SIZE = 16G SCOPE=BOTH;

    SHOW PARAMETER DB_FLASHBACK_FILE_DEST_SIZE

    NAME                                                             VALUE                                        
    ---------------------------------------------------------------- ----------------------------------------------------------------
    DB_FLASHBACK_FILE_DEST_SIZE                                      16G

    -- Set the maximum retention period for flashback log files to 7 days (10080 minutes)
    ALTER SYSTEM SET DB_FLASHBACK_RETENTION_TARGET = 10080 SCOPE=BOTH;

    SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'DB_FLASHBACK_%';

    NAME                                                             VALUE                                        
    ---------------------------------------------------------------- ----------------------------------------------------------------
    DB_FLASHBACK_RETENTION_TARGET                                    10080                                         
    DB_FLASHBACK_FILE_DEST_SIZE                                      16G
    ```

###  Backing Up and Restoring Flashback Log Files

Flashback log files are backed up as part of full database backups. For detailed steps, refer to [Backup and Recovery](../../../备份与恢复/00备份与恢复).


<span id="flashback_off" name="flashback_off" class="yaslink"></span>

##  Disabling Database Flashback

If database flashback is no longer required, disable it to restore full database performance.

Disabling flashback will automatically remove:

- Flashback log files (the `fra` directory remains).

- All non-guaranteed (normal) restore points.

- Flashback-related view data (e.g., GV$FLASHBACK_DATABASE_LOG, V$FLASHBACK_DATABASE_LOGFILE).

###  Prerequisites

- All [permanent restore points](./管理还原点) must be manually deleted first.

- The instance must be in MOUNT or OPEN state.

- The user must have SYSDBA privileges or FLASHBACK-related permissions.

###  Operation Steps

1. Connect and log in to the database with a user that meets the required permissions.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Execute the following statement to disable database flashback.

    ```sql
    ALTER DATABASE FLASHBACK OFF;
    ```
