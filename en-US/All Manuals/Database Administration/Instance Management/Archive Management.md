Archiving management mainly includes viewing and configuring the archiving mode, managing the archive path, cleaning up archive log files, and performing manual archiving operations.

<span id="check" name="check" class="yaslink"></span>

## View Archiving Mode

The log_mode field in the [V$DATABASE](../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE) view indicates the current archiving mode configuration of the database. A value of ARCHIVELOG means that archiving mode is enabled, while NOARCHIVELOG means that archiving mode is not enabled.

When the database operates in archiving mode, it automatically archives redo log files when they are full (that is, generating archive log files in the archive path). YashanDB also provides an automatic cleanup strategy for the archive log files.

Archiving can extend the retention time of the database's redo history operation records, which is used for [backup and recovery](../备份与恢复/00备份与恢复) to ensure data security and primary/standby synchronization during high availability deployment.

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```

2. Query the V$DATABASE view to obtain the archiving mode.

    ```sql
    SELECT database_name,log_mode,open_mode FROM V$DATABASE;
    
    DATABASE_NAME      LOG_MODE          OPEN_MODE  
    ------------------ ----------------- ------------
    yashandb              ARCHIVELOG        READ_WRITE 
    ```
    - LOG_MODE = ARCHIVELOG indicates that the archiving mode is enabled.

    - LOG_MODE = NOARCHIVELOG indicates that the archiving mode is disabled.

## Enable Archiving Mode

When creating the database, YashanDB provides parameters to configure the archiving mode (the ISARCHIVELOG parameter in *yasboot* is set to true|false, and the ARCHIVELOG|NOARCHIVELOG keywords are used in CREATE DATABASE). By default, YashanDB runs in archiving mode after installation without needing to be manually enabled.

After the archiving mode is enabled, database administrators can also adjust automatic cleanup strategies, archive paths, and other related configurations or perform [archive log files management](../存储管理/数据库文件管理/归档日志文件管理) based on actual business needs and disk planning.

### Prerequisites

- [Check current archiving mode](#check) is not enabled (i.e., `LOG_MODE = NOARCHIVELOG` in the `V$DATABASE` view).

- To enable archiving mode, the database needs to be started/restarted to the MOUNT state.



- When the database is in the MOUNT stage, only the sys user is allowed to log in.


- If you need to customize the storage path of archive log files (default is $YASDB_DATA/archive), configure the archive path before enabling archiving mode. The specified path will not be validated for existence during configuration, but it is recommended to plan and create the appropriate path before subsequent operations, and the system must have full privileges for that path.

### Operation Steps

<span id="archive_local_dest" name="archive_local_dest" class="yaslink"></span>

#### Step 1 (Optional): Customize Archive Path Configuration

The default archive path is $YASDB_DATA/archive, which can be customized using the ARCHIVE_LOCAL_DEST parameter.

If the path is adjusted after enabling the archiving mode, the archive log files that already exist in the original path will remain unchanged. They can be automatically or manually cleaned based on the cleanup rules. For cleanup operations, please refer to [archive log files management](../存储管理/数据库文件管理/归档日志文件管理.html#cleanup).

1. Log in to the database installation server using the installation user.


2. Check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```
    
    If the current database is in the OFF state (pid = off in the displayed information), it needs to be started at least to the NOMOUNT state before proceeding with subsequent operations.

    ```shell
    $ yasboot cluster start -c yashandb -m nomount
    ```

3. Connect and log in to the database.

    ```sql
    # If specifying to start in NOMOUNT stage, only the sys user can log in to the database
    $ yasql sys/********@192.168.1.2:1688

    # For other stages, it is recommended to log in to the database using the DBA user
    $ yasql sales/********@192.168.1.2:1688
    ```

4. View the current archive path.

    ```sql
    SHOW PARAMETER ARCHIVE_LOCAL_DEST;
    
    NAME                     VALUE        
    ------------------------ --------------
    ARCHIVE_LOCAL_DEST       ?/archive      
    ```

5. Modify the archive path.

    ```sql
    ALTER SYSTEM SET ARCHIVE_LOCAL_DEST='/home/yashan/archive' SCOPE=SPFILE;
    ```

#### Step 2: Restart to MOUNT State

1. If the current database is in the OPEN state, it is recommended to execute a checkpoint before stopping the service, to write the data in memory to disk.

    ```sql
    -- Check the database status
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    OPEN

    -- Execute checkpoint
    ALTER SYSTEM CHECKPOINT; 

    -- Exit yasql
    exit
    ```

2. Restart the database (or instance) to the MOUNT state.

    ::: tabs
    == Standalone Deployment, ISC Distributed Cluster Deployment 

```shell
$ yasboot cluster restart -c yashandb -m mount
```

    == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, to disable the archiving mode, only one instance needs to be in the MOUNT state while all other instances should be in the OFF state.

```shell
# Check the database instance startup method (AUTO_START parameter)
$ ycsctl get AUTO_START
YCS AUTO_START = ALWAYS
        
# Stop the database cluster
$ yasboot cluster stop -c yashandb

# Start the database instance on the current server to MOUNT stage
# If AUTO_START = ALWAYS (default value), execute the following command to directly start YCS instance and database instance
$ ycsctl start ycs -m mount

# If AUTO_START = NEVER, execute the following two commands to start YCS instance first, then the database instance
$ ycsctl start ycs 
$ ycsctl start instance -m mount
```
    :::




3. Log in to the database as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

4. Check the database status.

    ```sql
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    MOUNTED
    ```



5. View the current archive path configuration.
    
    ```sql
    SHOW PARAMETER ARCHIVE_LOCAL_DEST
    
    NAME                    VALUE         
    ----------------------- ----------------------
    ARCHIVE_LOCAL_DEST      /home/yashan/archive
    ```

#### Step 3: Adjust Archiving Mode

1. Adjust the database archiving mode.
    
    ```sql
    ALTER DATABASE ARCHIVELOG;
    ```

2. Check the archiving mode again to confirm the modification has taken effect.

    ```sql
    -- Check the current database's archiving mode again 
    SELECT database_name,log_mode,open_mode FROM V$DATABASE;
    
    DATABASE_NAME    LOG_MODE          OPEN_MODE   
    ---------------- ----------------- -------------
    yashandb            ARCHIVELOG        MOUNTED  
    ```

3. Start the database to OPEN.

    ::: tabs

    == Standalone Deployment, YAC/Distributed Cluster Deployment 
    
```sql
ALTER DATABASE OPEN;
```

    == ISC Distributed Cluster Deployment 

```shell
-- Exit yasql
SQL> exit

$ yasboot cluster restart -c yashandb
```
    :::



<span id="auto_cleanup_config" name="auto_cleanup_config" class="yaslink"></span>

## Clean Up Archive Log Files

<span id="auto_cleanup" name="auto_cleanup" class="yaslink"></span>

### View Automatic Cleanup Policy

YashanDB provides an automatic cleanup policy, including the cleanup strategy for archive log files and the triggering conditions for automatic cleanup tasks. Typically, no manual intervention is required if the relevant parameter configurations meet business requirements.

If YashanDB's default automatic cleanup policy does not meet actual needs, database administrators can adjust the corresponding parameters as needed or temporarily perform [manual cleanup](#auto_cleanup_config).

#### Cleanup Policy

Regardless of the method of cleanup, only archive log files that are no longer required by the database will be cleaned. The database maintains this principle on its own, and no additional configuration is required during cleanup. The criteria for not being required by the database can be referenced as follows:

- Earlier than the redo log application starting point: the sequence value of the archive log files is less than the ASN value of the RCY_POINT field in the [V$DATABASE](../../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE) view.

- In Standalone/YAC/Distributed Cluster Deployment, if a [permanent restore point](../故障处理/闪回/全库闪回/管理还原点) (CREATE RESTORE POINT … GUARANTEE FLASHBACK DATABASE) has been created, it must also be earlier than the permanent restore point: the NEXT_CHANGE# field value of the corresponding archive log files in the [V$ARCHIVED_LOG](../../参考手册/系统视图/动态视图/V$ARCHIVED_LOG) view must be less than or equal to the SCN field of the restore point in the [V$RESTORE_POINT](../../参考手册/系统视图/动态视图/V$RESTORE_POINT) view where IS_GUARANTEE is 1.

On top of the above principles, additional cleanup policies determined by the following configuration parameters ultimately result in files being cleaned only if they meet both the principles and policies.

|Parameter Name |Parameter Description |Default Value |
|--------------------|-------------------------------|-------|
|ARCHIVELOG_DELETION_POLICY | The cleanup policy for archive log files. When this parameter is not set to DEFAULT, it will invalidate the ARCH_CLEAN_IGNORE_MODE parameter.<br/><br/>* DEFAULT: indicates the cleanup policy follows the ARCH_CLEAN_IGNORE_MODE parameter configuration.<br/><br/>* BACKED UP integer TIMES: indicates that only archive log files that have been backed up the specified number of times integer are allowed to be cleaned. The integer is set within [1,15]. This can be specified alongside SHIPPED TO ALL STANDBY. The number of times an archive log file has been backed up can be confirmed via the BACKUP_COUNT field in the [V$ARCHIVED_LOG](../../Reference Manual/System Views/Dynamic Performance Views/V$ARCHIVED_LOG) view.<br/><br/>* SHIPPED TO ALL STANDBY: indicates that during primary/standby deployment, only archive log files synchronized to the standby database are allowed to be cleaned. This can also be specified with BACKED UP integer TIMES.<br/><br/>* NONE: Archive cleanup does not need to meet backup frequency or synchronization to the standby database, with behavior roughly equivalent to ARCH_CLEAN_IGNORE_MODE = BOTH.| DEFAULT |
| ARCH_CLEAN_IGNORE_MODE | The cleanup policy for archive log files. This parameter only takes effect when ARCHIVELOG_DELETION_POLICY is set to DEFAULT.<br/><br/>This parameter specifies which archive log files meet the cleanup conditions:<br/><br/>* BACKUP: indicates that backup is ignored, meaning archive log files are allowed to be cleaned regardless of whether they have been backed up. This can lead to the inability to recover the database to any point in time.<br/><br/>* STANDBY: indicates that during primary/standby deployment, the standby database is ignored, meaning archive log files are allowed to be cleaned regardless of whether they have been received by all standby databases. This setting might cause the standby database to fall behind the primary database and enter a need repair state.<br/><br/>* BOTH: indicates that both backup and standby database are ignored. This could cause the above two issues to occur.<br/><br/>* NONE: indicates that there are no ignores allowed; only archive log files that are backed up and received by all standby databases may be cleaned. | NONE |
| ARCH_CLEAN_UPPER_THRESHOLD | The triggering condition for automatic cleanup of archive log files. When the total size of all existing archive log files exceeds this value, it triggers automatic cleanup. The value range is [0,32T], and must not be less than ARCH_CLEAN_LOWER_THRESHOLD. If this parameter is set to 0, it disables automatic cleanup. This value only serves as a trigger for automatic cleanup; even if the total of archive log files exceeds this value, it does not affect normal transaction operations. | 16G |
| ARCH_CLEAN_LOWER_THRESHOLD | The stopping condition for automatic cleanup of archive log files. This parameter specifies the minimum value to retain for the archive space, and after one automatic cleanup of archives, the minimum total size of archive log files will be retained. The value range is [0,32T], and if this parameter is set to 0, it cleans all archive log files that can be cleaned. | 12G |

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the relevant parameter configurations.

    ```sql
    SELECT NAME,VALUE FROM V$PARAMETER WHERE ( NAME LIKE 'ARCH_CLEAN_%' ) OR ( NAME = 'ARCHIVELOG_DELETION_POLICY');

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCH_CLEAN_UPPER_THRESHOLD                                       16G
    ARCH_CLEAN_LOWER_THRESHOLD                                       12G
    ARCH_CLEAN_IGNORE_MODE                                           NONE
    ARCHIVELOG_DELETION_POLICY                                       DEFAULT
    ```
    

#### Cleanup Process

The default process for the automatic cleanup of archive log files is as follows (the following descriptions use default parameter values as examples):

1. The system will regularly check if the triggering conditions for the automatic cleanup task are met by comparing the total size of all existing archive log files against the ARCH_CLEAN_UPPER_THRESHOLD parameter.

2. When the total size of all existing archive log files ≥ 16G, the automatic cleanup task for archive log files is triggered.

3. The system checks if the archive log files with the smallest sequence meet the cleanup principles and strategies:

    - If the file meets the criteria of not being needed by the database any longer + has been backed up + has been received by all standby databases, cleanup is executed.

    - If it does not meet all three conditions, it will not be cleaned, and a log will be printed to record the operation.

    Even if all files do not meet the cleanup strategy, it will not affect database operation until the disk is full.

4. After determining/processing one file, the system checks whether the stopping conditions for the automatic cleanup task have been met by comparing the total size of all existing archive log files to the ARCH_CLEAN_LOWER_THRESHOLD parameter.

    If the total size of all existing archive log files is still greater than 12G, then it checks whether the next file can be cleaned based on the sequence number of the archive log files, repeating the process.

5. The automatic cleanup task will stop when the total size of all existing archive log files is ≤ 12G.

### Adjust Automatic Cleanup Strategy

#### Precautions

- YashanDB supports restoring the database by specifying a point in time. If archive log files generated after a backup set and the online logs are continuous and complete, the database can continue to be recovered to any point in time by specifying that point. If the archive log files after the backup set are cleaned up, the database will not be able to continue recovery to any point in time.

- In high availability deployment scenarios, it should also be noted:

    - The standby database needs to sync data from the primary database by obtaining the archive log files. If the corresponding archive log files have been cleaned before the standby database obtains them, it will result in the standby database being unable to synchronize data correctly.

    - If the configuration is ARCHIVELOG_DELETION_POLICY = 'BACKED UP integer TIMES', the archive log files on the main and standby databases must each be backed up the specified number of times before they can be cleaned. It is recommended to set different archival cleanup strategies for the primary and standby databases to avoid duplicate backups of the primary database's archive log files, issues with archive log files not being cleaned in a timely manner, and wasting disk space.

    - If the primary database is configured with ARCHIVELOG_DELETION_POLICY = 'SHIPPED TO ALL STANDBY', a downtime of the standby database will cause the primary database's archive log files to not be cleaned in time.

- Setting the ARCH_CLEAN_UPPER_THRESHOLD parameter to 0 means turning off automatic cleanup. To avoid wasted disk space, it is not recommended to disable automatic cleanup of archive log files unless necessary.

#### Operation Steps

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the relevant parameter configurations.

    ```sql
    SELECT NAME,VALUE FROM V$PARAMETER WHERE ( NAME LIKE 'ARCH_CLEAN_%' ) OR ( NAME = 'ARCHIVELOG_DELETION_POLICY');

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCH_CLEAN_UPPER_THRESHOLD                                       16G
    ARCH_CLEAN_LOWER_THRESHOLD                                       12G
    ARCH_CLEAN_IGNORE_MODE                                           NONE
    ARCHIVELOG_DELETION_POLICY                                       DEFAULT
    ```
    

3. Adjust parameter values as needed and specify the scope of effectiveness.

    ```sql
    -- Configure to allow archive log files to be cleaned only after being synchronized to all standby databases and backed up 2 times (this will invalidate the ARCH_CLEAN_IGNORE_MODE parameter)
    ALTER SYSTEM SET ARCHIVELOG_DELETION_POLICY = 'SHIPPED TO ALL STANDBY, BACKED UP 2 TIMES' SCOPE=BOTH;

    SHOW PARAMETER ARCHIVELOG_DELETION_POLICY

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCHIVELOG_DELETION_POLICY                                       SHIPPED TO ALL STANDBY, BACKED UP 2 TIMES

    -- Set the triggering condition for automatic cleanup of archive log files to 20G
    ALTER SYSTEM SET ARCH_CLEAN_UPPER_THRESHOLD = 20G SCOPE=BOTH;

    SELECT NAME,VALUE FROM V$PARAMETER WHERE NAME LIKE 'ARCH_CLEAN_%_THRESHOLD';

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    ARCH_CLEAN_UPPER_THRESHOLD                                       20G                                                    
    ARCH_CLEAN_LOWER_THRESHOLD                                       12G
    ```

## Manual Archiving

Typically, the automatic archiving mechanism of the database can meet normal operational needs. However, in certain specific scenarios, database administrators may perform manual archiving as needed, such as:

- Before backups or migration, to improve the completeness and consistency of the backup (for example, to make the upcoming backup set closer to the current business data), it may be beneficial to execute manual archiving before proceeding with the operations.

- When testing archive configurations, especially in the early stages after enabling archiving mode, manual archiving may be performed to verify that archive log files are generated correctly.

- During database performance optimization, it may be necessary to manually archive to ensure log files are allocated and stored as expected.

>**Caution**:
>
> In production environments, reliance on the automatic archiving mechanism should be prioritized, and manual intervention should only occur in special circumstances.
>
> Frequent manual archiving should be avoided to prevent increased I/O pressure.

Manual archiving ([ALTER SYSTEM ARCHIVE LOG CURRENT](../../开发手册/SQL参考手册/SQL语句/ALTER SYSTEM.html#archive_logfile)) can only archive redo log files that are in the CURRENT state. During the archiving process, the redo log files are also switched. For specific operations, refer to [switching and archiving redo log files](../存储管理/数据库文件管理/redo日志文件管理.html#SWITCH).

The archive log files generated through manual archiving are managed the same way as those generated through automatic archiving, and are subject to the same automatic cleanup policy. They can be managed as needed, following [file management](../存储管理/数据库文件管理/归档日志文件管理).

## Disable Archiving Mode

In production environments, it is **not recommended** to disable archiving mode, and the database must run in archiving mode in the following scenarios:

- High availability deployments, such as standalone primary-standby deployment or primary-standby YAC/Distributed Cluster Deployment.

- Need to use [YStream](../../开发手册/YStream参考手册/00YStream参考手册).

- Need to use [database flashback](../故障处理/闪回/00闪回).

### Prerequisites

- The REPLICATION_ADDR parameter has been confirmed empty.  

    ```sql
    -- Check parameter value
    SHOW PARAMETER REPLICATION_ADDR;

    -- If the parameter value is not empty, it must be cleared to disable archive mode 
    ALTER SYSTEM SET REPLICATION_ADDR ='' SCOPE=SPFILE;
    ```

- [Check current archive mode](#check) is enabled (i.e., `LOG_MODE = ARCHIVELOG` in the `V$DATABASE` view).

- To disable archiving mode, the database needs to be started/restarted to the MOUNT state.

### Operation Steps

1. Restart the database (or instance) to the MOUNT state.

    ::: tabs
    == Standalone Deployment, ISC Distributed Cluster Deployment 

```shell
$ yasboot cluster restart -c yashandb -m mount
```

    == YAC/Distributed Cluster Deployment 
    
In YAC/Distributed Cluster Deployment, to disable the archiving mode, only one instance needs to be in the MOUNT state while all other instances should be in the OFF state.

```shell
# Check the database instance startup method (AUTO_START parameter)
$ ycsctl get AUTO_START
YCS AUTO_START = ALWAYS
        
# Stop the database cluster
$ yasboot cluster stop -c yashandb

# Start the database instance on the current server to MOUNT stage
# If AUTO_START = ALWAYS (default value), execute the following command to directly start YCS instance and database instance
$ ycsctl start ycs -m mount

# If AUTO_START = NEVER, execute the following two commands to start YCS instance first, then the database instance
$ ycsctl start ycs 
$ ycsctl start instance -m mount
```
    :::



2. Log in to the database as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

3. Check the database status.

    ```sql
    SELECT status FROM V$INSTANCE;

    STATUS
    -------------
    MOUNTED
    ```


4. Adjust the database archiving mode.
    
    ```sql
    ALTER DATABASE NOARCHIVELOG;
    ```

5. Check the archiving mode again to confirm the modification has taken effect.

    ```sql
    -- Check the current database's archiving mode again 
    SELECT database_name,log_mode,open_mode FROM V$DATABASE;
    
    DATABASE_NAME    LOG_MODE          OPEN_MODE   
    ---------------- ----------------- -------------
    yashandb            NOARCHIVELOG      MOUNTED  
    ```

6. Start the database to OPEN.

    ::: tabs

    == Standalone Deployment, YAC/Distributed Cluster Deployment 
    
```sql
ALTER DATABASE OPEN;
```

    == ISC Distributed Cluster Deployment 

```shell
-- Exit yasql
SQL> exit

$ yasboot cluster restart -c yashandb
```
    :::


