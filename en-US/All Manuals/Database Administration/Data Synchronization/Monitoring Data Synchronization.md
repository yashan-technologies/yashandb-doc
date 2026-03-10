After configuring the logical standby database, the DBA should monitor and manage the daily usage of the logical standby database through the DBA_LOGSTDBY_EVENTS and V$DIAG_INCIDENT views.

##  Related Views

### DBA\_LOGSTDBY\_EVENTS

The [DBA_LOGSTDBY_EVENTS](../../参考手册/系统视图/DBA视图/DBA_LOGSTDBY_EVENTS) view records the following information:

- Events related to SQL apply blocking due to resource errors (for example, insufficient tablespace, insufficient CURSOR POOL, etc.) on the logical standby database. If such events occur, DBA intervention is required for remediation.

- Operations that are directly skipped during SQL apply on the logical standby database due to primary key update conflicts, which may lead to data inconsistency with the primary database.

### V$DIAG\_INCIDENT
   
If there is insufficient STREAM POOL space, the SQL apply on the logical standby database will be interrupted, and the database will log and report the incident (in the V$DIAG_INCIDENT view). At this point, DBA intervention is needed to adjust the size of the STREAM POOL and restart the SQL apply.

### V$LOGSTDBY\_PROGRESS

The [V$LOGSTDBY_PROGRESS](../../参考手册/系统视图/动态视图/V$LOGSTDBY_PROGRESS) view records the replay progress of the logical standby database.

##  Common Issues and Solutions

###  SQL Replay Blocked Due to Insufficient Tablespace

**Symptom**

Logical standby database reports error "YAS-02007 no free extent in tablespace XXX", SQL replay stalls, and the data synchronization delay of the logical standby database continues to increase.

**Solution**

1. Expand the tablespace:

    ```sql
    -- Method 1: When creating a tablespace, enable automatic data file extension (enabled by default), and this attribute cannot be adjusted after creation
    CREATE TABLESPACE yashan2 DATAFILE 'yashan2' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G EXTENT UNIFORM SIZE 64K; 

    -- Method 2: Add data files for the existing tablespaces
    ALTER TABLESPACE yashan ADD DATAFILE;
    ```

2. Optimize monitoring, focus more on tablespace usage, and detect capacity issues in time to avoid blocking again.

    ```sql
    SELECT tablespace_name,user_bytes,user_blocks FROM DBA_TABLESPACES;
    ```

###  SQL Replay Blocked Due to Insufficient CURSOR POOL

**Symptom**

Logical standby database reports error "YAS-02009 no free cursors in cursor pool", SQL replay speed drops sharply or even stalls.

**Solution**

1. Include the cursor open count in the daily monitoring metrics to facilitate timely detection and closure of unnecessary cursors.

    ```sql
    -- Confirm the upper limit of cursors allowed per session and set this parameter reasonably
    SHOW PARAMETER OPEN_CURSORS;
    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    OPEN_CURSORS                                                     310

    -- Monitor the number of opened cursors
    SELECT name,value FROM V$SYSSTAT WHERE name='OPENED CURSORS CURRENT';

    NAME                                                                             VALUE
    ---------------------------------------------------------------- ---------------------
    OPENED CURSORS CURRENT                                                               1
    ```

2. Optimize application code, such as:
    
    1. ) Review the business code to ensure that every cursor opened has a corresponding close operation.
    
    2. ) Use bind variables as much as possible to reduce hard parsing and decrease cursor occupation.

3. Increase the CURSOR_POOL_SIZE configuration of the logical standby database as needed, requiring a database restart to take effect.

    ```sql
    ALTER SYSTEM SET CURSOR_POOL_SIZE=2T scope=spfile;  
    ```

###  SQL Replay Blocked Due to Insufficient STREAM POOL Space

**Symptom**

Logical standby database reports error "YAS-00103 no free block in stream pool", SQL replay stalls, and archived logs accumulate.

**Solution**

1. Increase the STREAM_POOL_SIZE configuration of the logical standby database as needed.

    ```sql
    -- It is recommended to configure STREAM_POOL_SIZE using a percentage of the maximum Share Pool
    ALTER SYSTEM SET STREAM_POOL_SIZE=55 scope=spfile;  
    ```

2. If STREAM_POOL_SIZE is configured as a percentage of the maximum Share Pool, the SHARE_POOL_SIZE of the logical standby database can also be increased as needed.

    ```sql 
    ALTER SYSTEM SET SHARE_POOL_SIZE=2T scope=spfile;  
    ```
