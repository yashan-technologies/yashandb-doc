## Location of Data Files

By default, data files are stored in the `$YASDB_DATA/dbfiles` directory (SCOL data is stored in the `local_fs` directory at the same level as data).

In YAC/Distributed Cluster Deployment mode, data files are stored in the `+DG0/dbfiles` directory. Please refer to [YFS File Management](../../存储管理/集群文件系统管理/文件管理) for access methods.

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

***Example*** for YAC/Distributed Cluster Deployment

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

***Example*** for YAC/Distributed Cluster Deployment

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

In Standalone/YAC/Distributed Cluster Deployment, you can directly use the [ALTER DATABASE](../../../开发手册/SQL参考手册/SQL语句/ALTER DATABASE) statement. In ISC Distributed Cluster Deployment, it is recommended to use the [yasboot](../../../工具手册/yasboot/yasboot命令介绍/00yasboot命令介绍) operation and maintenance tool.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER DATABASE DATAFILE 'datafile' OFFLINE;

-- When archiving is not enabled on the database, DROP must be specified
ALTER DATABASE DATAFILE 'datafile' OFFLINE DROP;
```