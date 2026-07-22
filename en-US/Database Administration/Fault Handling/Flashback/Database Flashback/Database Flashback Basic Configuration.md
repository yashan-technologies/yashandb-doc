The basic configuration for database flashback includes enabling/disabling the functionality and managing flashback log files.

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the flashback operations in the CDB root and each PDB — along with their associated flashback log files, restore points, and other dependencies — are independent and private. You must connect to the target container to perform the related management operations, and such operations only take effect on that specific container.

The database flashback functionality is unavailable in ISC Distributed Cluster Deployment.

##  Enabling Database Flashback

Database flashback is disabled by default and can be enabled based on operational requirements.

> **Note**:
>
> - The database flashback functionality consumes some system resources. Please enable it as appropriate in scenarios permitted by the business.
>
> - After enabling database flashback, flashback logs will be automatically generated, and the size of these flashback logs will increase as the database runs. You can [configure](#auto_cleanup) the upper limit of the disk capacity that flashback log files are allowed to occupy according to your needs and allocate sufficient disk space for them.

###  Prerequisites

- The database must be in [archivelog mode](../../../Instance Management/Archive Management).

- The database (or container) instance on which the operation is performed must be in the MOUNT or OPEN state. If it is in the MOUNT state, only the sys user can log in at this time.

- When performing this operation on a standby cluster in Primary-Standby Cluster Deployment, you must log in to its master instance (INSTANCE_ROLE=MASTER in the GV$INSTANCE view).

- The user must have SYSDBA privileges or FLASHBACK-related permissions. 

###  Operation Steps

1. Connect and log in to the target database (or container) with a user that meets the required permissions.

    ```shell
    # Log in to a non-CDB or the CDB root of a CDB
    $ yasql / as sysdba

    # Log in to a PDB in a CDB
    $ yasql sys/********@192.168.1.2:1688/pdb1
    ```
    

2. The FLASHBACK_ON column in the [V$DATABASE](../../../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE) view indicates whether the global flashback functionality is enabled.

    ```sql
    SELECT FLASHBACK_ON FROM V$DATABASE;

    FLASHBACK_ON
    -----------------
    NO
    ```
    - FLASHBACK_ON = NO indicates flashback is disabled. Proceed to enable it as needed.

    - FLASHBACK_ON = YES indicates flashback is enabled.
    

3. Configure flashback log-related parameters as needed: 

    - Log storage path: Configure via the DB_FLASHBACK_FILE_DEST parameter. You can adjust this parameter to specify a custom path for flashback log storage.

        - In Standalone Deployment, the default is `$YASDB_DATA/fra`.

        - In YAC/Distributed Cluster Deployment, the default is `+DG0/fra`.

        - When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the above defaults apply to the CDB root; for PDBs, the default path is `{PDB_data_file_path}/fra` (e.g., `$YASDB_DATA/containers/{pdb_name}/fra` or `+DG0/containers/{pdb_name}/fra`). If you need to adjust the configuration, the `DB_FLASHBACK_FILE_DEST` parameter can only be customized by directly connecting to the target container, and the change will only take effect for that specific container.

    - Automatic log cleanup policy: Configure via the DB_FLASHBACK_FILE_DEST_SIZE and DB_FLASHBACK_RETENTION_TARGET parameters. For more details, refer to [Managing Flashback Log Auto-Purge Policies](#auto_cleanup).

    ```sql
    -- View current configuration
    SELECT NAME,VALUE FROM  V$PARAMETER WHERE NAME LIKE 'DB_FLASHBACK_%';

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    DB_FLASHBACK_RETENTION_TARGET                                    1440                                                   
    DB_FLASHBACK_FILE_DEST_SIZE                                      8G                                                     
    DB_FLASHBACK_FILE_DEST                                           ?/fra
    ```

    If the current configuration does not meet actual requirements, it can be adjusted as needed. This example uses the default path.

4. If the current logged-in entity is a standby database/standby cluster, the redo log apply should be paused before proceeding with subsequent operations.

    ```sql
    -- Check the database status
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    OPEN
    
    -- If it is in the OPEN state, manually stop redo log apply
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;

    -- If it is in the MOUNTED state, its redo log application has not started yet (no additional operation is required, directly proceed to the next step).
    ```
    

5. Execute the following statement to enable database flashback: 

    ```sql
    ALTER DATABASE FLASHBACK ON;
    ```

6. If the redo log application was stopped in the previous steps, it needs to be resumed.

    ```sql
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
    ```
    
    To reduce repetitive operations, you may also consider resuming it after [creating a restore point](./Managing Restore Points).
    
##  Viewing Database Flashback Information

- The FLASHBACK_ON column in the [V$DATABASE](../../../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE) view indicates whether the global flashback functionality is enabled.

    ```sql
    SELECT FLASHBACK_ON FROM V$DATABASE;

    FLASHBACK_ON
    -----------------
    NO
    ```
    - FLASHBACK_ON = NO indicates flashback is disabled. Proceed to enable it as needed.

    - FLASHBACK_ON = YES indicates flashback is enabled.
    

- Use the [GV$FLASHBACK_DATABASE_LOG](../../../../Reference Manual/System Views/Dynamic Performance Views/GV$FLASHBACK_DATABASE_LOG) or [V$FLASHBACK_DATABASE_LOG](../../../../Reference Manual/System Views/Dynamic Performance Views/V$FLASHBACK_DATABASE_LOG) views to retrieve database flashback details, such as the oldest recoverable SCN and the storage space occupied by flashback log files.

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

The [V$FLASHBACK_DATABASE_LOGFILE](../../../../Reference Manual/System Views/Dynamic Performance Views/V$FLASHBACK_DATABASE_LOGFILE) view provides information about flashback log files, including filenames, file sizes, and the SCN when files were created.

```sql
SELECT * FROM V$FLASHBACK_DATABASE_LOGFILE;

FILE_NAME                                        FILE_NUM THREAD#    SEQUENCE#             FILE_SIZE         FIRST_CHANGE# FIRST_TIME
-------------------------------------------- ------------ ------- ------------ --------------------- --------------------- -----------------------------
$YASDB_DATA/fra/flb_file_0                             0       0            0            1074827264    723743061460926464 2025-08-07 17:58:03.364484
$YASDB_DATA/fra/flb_file_1                             1       0            1             141250048    724923208865271808 2025-08-11 02:00:05.289373
```

== YAC/Distributed Cluster Deployment

The [GV$FLASHBACK_DATABASE_LOGFILE](../../../../Reference Manual/System Views/Dynamic Performance Views/GV$FLASHBACK_DATABASE_LOGFILE)/[V$FLASHBACK_DATABASE_LOGFILE](../../../../Reference Manual/System Views/Dynamic Performance Views/V$FLASHBACK_DATABASE_LOGFILE) views provide information about flashback log files, including filenames, file sizes, and the SCN when files were created.

```sql
SELECT INST_ID,FILE_NAME,FILE_NUM,THREAD#,SEQUENCE#,FILE_SIZE,FIRST_CHANGE#,FIRST_TIME FROM GV$FLASHBACK_DATABASE_LOGFILE;

    INST_ID FILE_NAME                           FILE_NUM THREAD#    SEQUENCE#             FILE_SIZE         FIRST_CHANGE# FIRST_TIME
----------- ------------------------------- ------------ ------- ------------ --------------------- --------------------- --------------------------------
          2 +DG0/fra/flb_file_1_0                      0       1            0                 59392    725391921513275392 2025-08-12 09:47:17.088202
          1 +DG0/fra/flb_file_0_0                      0       0            0                 67584    725391913422884864 2025-08-12 09:47:15.113009
```
:::


<span id="auto_cleanup" name="auto_cleanup"></span>

###  Managing Flashback Log Auto-Purge Policies

YashanDB provides automatic flashback log purge policies, including cleanup rules and trigger conditions for auto-purge tasks.

If the default auto-purge policies do not meet requirements, **only modify parameter values—do not manually delete physical files**.

When automatically cleaning up flashback logs, only files earlier than [permanent restore points](./Managing Restore Points) are removed.

#### Cleanup Policy

The purge policy depends on the following parameters: 

|Parameter Name |Parameter Description |Default Value |
|--------------------|-------------------------------|-------|
| DB_FLASHBACK_FILE_DEST_SIZE  | Maximum allowed disk space for flashback log files (in bytes). <br/> It also serves as the trigger condition for the flashback log file cleanup task — after the cleanup task is triggered, all flashback log files that are older than the permanent restore point and also meet the maximum retention period (controlled by the DB_FLASHBACK_RETENTION_TARGET parameter) requirement will be deleted. | 8G |
| DB_FLASHBACK_RETENTION_TARGET  | Maximum retention period for flashback log files (in minutes).<br/>It also serves as the cleanup policy for flashback log files (i.e., whether they are eligible for cleanup). | 1440 |

>**Caution**：
>
> If the automatic cleanup task cannot ensure that the total capacity of all existing flashback log files is ≤ the value of the DB_FLASHBACK_FILE_DEST_SIZE parameter — for example, if no flashback log files meet the cleanup criteria and thus cannot be automatically cleaned — the business may become blocked. The system will print an operational log indicating insufficient space, requiring manual intervention to either increase the value of the DB_FLASHBACK_FILE_DEST_SIZE parameter or manually delete unnecessary [permanent restore points](./Managing Restore Points).

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

<span id="flashback_off" name="flashback_off"></span>

##  Disabling Database Flashback

If database flashback is no longer required, disable it to restore database performance.

When disabling database flashback, the following information will be automatically deleted/cleared. Please **operate with caution**.

- All existing flashback log files, but the `fra` directory remains.

- All non-guaranteed (normal) restore points.

- Flashback-related view data (e.g., GV$FLASHBACK_DATABASE_LOG/V$FLASHBACK_DATABASE_LOG, GV$FLASHBACK_DATABASE_LOGFILE/V$FLASHBACK_DATABASE_LOGFILE).

###  Prerequisites

- All [permanent restore points](./Managing Restore Points) must be manually deleted first.

- The database (or container) instance on which the operation is performed must be in the MOUNT or OPEN state.

- The user must have SYSDBA privileges or FLASHBACK-related permissions.

###  Operation Steps

1. Connect and log in to the target database (or container) with a user that meets the required permissions.

    ```shell
    # Log in to a non-CDB or the CDB root of a CDB
    $ yasql / as sysdba

    # Log in to a PDB in a CDB
    $ yasql sys/********@192.168.1.2:1688/pdb1
    ```
    

2. Execute the following statement to disable database flashback.

    ```sql
    ALTER DATABASE FLASHBACK OFF;
    ```
