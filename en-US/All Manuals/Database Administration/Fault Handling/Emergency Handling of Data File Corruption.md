When a data file is corrupted, it can usually be resolved by [restoring](../备份与恢复/00备份与恢复) the latest available backup. If no backup exists, emergency measures need to be taken based on this operation.

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
