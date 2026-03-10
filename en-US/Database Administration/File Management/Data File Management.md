## Location of Data Files

By default, data files are stored in the `$YASDB_DATA/dbfiles` directory (SCOL data is stored in the `local_fs` directory at the same level as data).

In YAC Deployment mode, data files are stored in the `+DG0/dbfiles` directory. Please refer to [YFS File Management](../../YashanDB for Cluster/Yashan File System/File Management) for access methods.

```shell
$ cd $YASDB_DATA/dbfiles
$ ls -lrt
total 1122904
...
-rw-r----- 1 yashan yashan 134217728 Oct 19 11:50 redo1
-rw-r----- 1 yashan yashan 134217728 Oct 19 14:15 redo2
-rw-r----- 1 yashan yashan 134217728 Oct 19 11:50 redo3
-rw-r----- 1 yashan yashan 134217728 Oct 19 11:50 redo4
-rw-r----- 1 yashan yashan  67108864 Oct 19 11:50 temp
-rw-r----- 1 yashan yashan  67108864 Oct 19 11:50 swap
-rw-r----- 1 yashan yashan  67108864 Oct 19 11:50 users
-rw-r----- 1 yashan yashan  67108864 Oct 19 14:00 sysaux
-rw-r----- 1 yashan yashan  67108864 Oct 19 14:15 system
-rw-r----- 1 yashan yashan 134217728 Oct 19 14:15 undo
-rw-r----- 1 yashan yashan  67108864 Oct 19 14:15 dwf
-rw-r----- 1 yashan yashan  25370624 Oct 19 14:15 ctrl1
-rw-r----- 1 yashan yashan  25370624 Oct 19 14:15 ctrl2
-rw-r----- 1 yashan yashan  25370624 Oct 19 14:15 ctrl3
...
```
The types of files vary slightly in different deployment scenarios:

- redo: redo log files
- temp, swap, users, sysaux, system, undo, users_aim-0 (unique to ISC Distributed Cluster Deployment): tablespace data files; TSS_*_CHUNK_*_FILE_0 (unique to ISC Distributed Cluster Deployment): tablespace data files containing chunks
- dwf (Standalone Deployment), double_write (ISC Distributed Cluster Deployment): double write files
- ctrl: control files

## Viewing Data Files

In addition to directly accessing the directory where data files are stored in the operating system, data files can also be viewed through the DBA_DATA_FILES and V$DATAFILE views as follows:

***Example*** for Standalone Deployment

```sql
SELECT file_name FROM DBA_DATA_FILES;

FILE_NAME                                                        
---------------------------------------------------------------- 
/data/yashan/yasdb_data/db-1-1/dbfiles/system                    
/data/yashan/yasdb_data/db-1-1/dbfiles/sysaux                    
/data/yashan/yasdb_data/db-1-1/dbfiles/undo                      
/data/yashan/yasdb_data/db-1-1/dbfiles/temp                      
/data/yashan/yasdb_data/db-1-1/dbfiles/swap                      
/data/yashan/yasdb_data/db-1-1/dbfiles/users  

SELECT name FROM V$DATAFILE;

NAME                                                             
---------------------------------------------------------------- 
/data/yashan/yasdb_data/db-1-1/dbfiles/system                    
/data/yashan/yasdb_data/db-1-1/dbfiles/sysaux                    
/data/yashan/yasdb_data/db-1-1/dbfiles/undo                      
/data/yashan/yasdb_data/db-1-1/dbfiles/temp                      
/data/yashan/yasdb_data/db-1-1/dbfiles/swap                      
/data/yashan/yasdb_data/db-1-1/dbfiles/users 
```

***Example*** for YAC Deployment

```sql
SELECT file_name FROM DBA_DATA_FILES;

FILE_NAME                                                        
---------------------------------------------------------------- 
+DG0/dbfiles/system                                             
+DG0/dbfiles/sysaux                                             
+DG0/dbfiles/temp                                               
+DG0/dbfiles/swap                                               
+DG0/dbfiles/users                                              
+DG0/dbfiles/undo1                                              
+DG0/dbfiles/undo2                                              
+DG0/dbfiles/undo3                                              

SELECT name FROM V$DATAFILE;

NAME                                                             
---------------------------------------------------------------- 
+DG0/dbfiles/system                                             
+DG0/dbfiles/sysaux                                             
+DG0/dbfiles/temp                                               
+DG0/dbfiles/swap                                               
+DG0/dbfiles/users                                              
+DG0/dbfiles/undo1                                              
+DG0/dbfiles/undo2                                              
+DG0/dbfiles/undo3                                              
```

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT file_name FROM DBA_DATA_FILES;

FILE_NAME
----------------------------------------------------------------
/data/yashan/yasdb_data/cn-2-1/dbfiles/system
/data/yashan/yasdb_data/cn-2-1/dbfiles/sysaux
/data/yashan/yasdb_data/cn-2-1/dbfiles/temp
/data/yashan/yasdb_data/cn-2-1/dbfiles/swap
/data/yashan/yasdb_data/cn-2-1/dbfiles/users
/data/yashan/yasdb_data/cn-2-1/dbfiles/undo
/data/yashan/yasdb_data/cn-2-1/dbfiles/users_aim-0

SELECT name FROM V$DATAFILE;

NAME
----------------------------------------------------------------
/data/yashan/yasdb_data/cn-2-1/dbfiles/system
/data/yashan/yasdb_data/cn-2-1/dbfiles/sysaux
/data/yashan/yasdb_data/cn-2-1/dbfiles/temp
/data/yashan/yasdb_data/cn-2-1/dbfiles/swap
/data/yashan/yasdb_data/cn-2-1/dbfiles/users
/data/yashan/yasdb_data/cn-2-1/dbfiles/undo
/data/yashan/yasdb_data/cn-2-1/dbfiles/users_aim-0
```

Through the DBA_DATA_FILES and V$DATAFILE views, other attributes of the data file can also be viewed, such as file number, file size, corresponding tablespace, and file status.

## Maintenance of Data Files

### Adding Data Files

A tablespace can contain multiple data files. When the space of a tablespace is exhausted or a data file reaches its maximum size, additional data files can be added by extending the tablespace.

Adding a data file when creating a new tablespace:

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
CREATE TABLESPACE ts_yashan DATAFILE '?/data01' SIZE 100M AUTOEXTEND OFF;
```

***Example*** for YAC Deployment

```sql
CREATE TABLESPACE ts_yashan DATAFILE '+DG0/ts_yashan' SIZE 100M AUTOEXTEND OFF;
```

Adding data files by extending a tablespace:

***Example***

```sql
ALTER TABLESPACE yashan ADD DATAFILE;
```

### Taking Data Files Offline

In some scenarios, such as when a data file is corrupted, no longer needed, or needs to be safely isolated, it may be necessary to take the specified data file offline (offline) (data files of built-in tablespaces cannot perform this operation).

When the file is in normal use, the data will still be backed up after the file is taken offline. Illegal operations such as renaming the file or externally deleting it are not allowed, otherwise it will lead to backup failure.

In cases of file corruption or other exceptions, the file can only be taken offline when the database is in MOUNT state; in other cases, it is recommended to take it offline when in OPEN state.

In a primary/standby environment, it is recommended to execute offline on the primary database; the standby database will automatically synchronize the offline operation. Executing offline on the standby database will cause the standby database to appear with a need repair exception.

> **Warn**:
> 
> Please proceed with caution on production systems. If the offline time of the data file is too long and the archive files generated during this period are lost, the file cannot be brought online, resulting in inability to use it normally in the future.
> 
> After this operation is performed, the tablespace to which this data file belongs will be set to OFFLINE status, and all other data files in this tablespace will be set to RECOVER status.


In Standalone Deployment and YAC Deployment, you can directly use the [ALTER DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER DATABASE) statement. In ISC Distributed Cluster Deployment, it is recommended to use the [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/00Introduction to yasboot Command) operation and maintenance tool.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER DATABASE DATAFILE 'datafile' OFFLINE;

-- When archiving is not enabled on the database, DROP must be specified
ALTER DATABASE DATAFILE 'datafile' OFFLINE DROP;
```

<span id="EH" name="EH" class="yaslink"></span>

## Emergency Handling of Data File Corruption

When a data file is corrupted, it can usually be resolved by [restoring](../Backup and Recovery/00Backup and Recovery) the latest available backup. If no backup exists, emergency measures need to be taken based on this operation.

### Common Scenarios

A data file under a tablespace in the database is missing, corrupted, etc., causing the database to fail to start normally, and no available backup exists.

> **Note**: 
>
> The common scenarios, solutions, and operational steps described in this article do not include data files of built-in tablespaces. If a built-in tablespace data file is lost or corrupted and no available backup exists, physical recovery or other measures should be used.

### Solutions

Enable the escape route, take the lost or corrupted data file offline while the database is in mount mode, and then open the database.

### Operational Steps

In a primary/standby environment, relevant operations should be executed on the primary database, which will cause the corresponding data files on the standby database to be taken offline through primary/standby synchronization. Performing relevant operations directly on the standby database will cause its status to be set to need repair, making it unusable.

1. Identify the abnormal data file through the error message or log during database startup:

    ```shell
    $ open file /home/yashan/YASDB_DATA/dbfiles/SALES0 failed, errno 2, error message "No such file or directory"
    Failed to start instance
    ```

    Alternatively, when the database is found to be in abnormal status, query V$DIAG_INCIDENT to investigate if the abnormal condition was caused by corruption of a file.

2. Start the database in mount state:

    ```shell
    $ yasboot cluster restart -c yashandb -m mount
    ```

3. Take the abnormal data file offline:

    When the database is not archiving, the DROP option must be specified; otherwise, it is not required.

    ```sql
    ALTER DATABASE DATAFILE 'SALES0' OFFLINE DROP;
    ```

4. At this point, the database can be opened normally, and the tablespace corresponding to the data file is offline:

    ```sql
    ALTER DATABASE OPEN;
    
    SELECT tablespace_name, status FROM DBA_TABLESPACES WHERE tablespace_name='SALES'; 
    TABLESPACE_NAME                                                  STATUS    
    ---------------------------------------------------------------- --------- 
    SALES                                                            OFFLINE  
    ```

5. A prompt error is displayed when reading the abnormal data file:

    ```sql
    SELECT * FROM area;
    YAS-02247 file 6 can not be read at this time
    ```
