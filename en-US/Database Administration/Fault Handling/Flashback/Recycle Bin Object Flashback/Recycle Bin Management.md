##  Enabling the Recycle Bin

The recycle bin is controlled by the configuration parameter RECYCLEBIN_ENABLED, which defaults to ON (enabled).

###  Precautions

- During configuration changes, avoid concurrent DROP/TRUNCATE operations, as the recycle bin may not yet be fully enabled, leading to permanent object deletion.



- In YAC/Distributed Cluster Deployment, recycle bin configurations across multiple instances will not be automatically synchronized. It is recommended to maintain consistent configurations; otherwise, only instances with the recycle bin enabled will move HEAP table data to the recycle bin when deletion operations are performed. To adjust recycle bin enable/disable settings, you must connect to each instance individually to perform the corresponding operations.

- In high availability (HA) deployments, the configuration is not synchronized between primary and standby databases (or clusters), and only the primary database's (or primary cluster's) configuration takes effect. Ensure consistent settings on primary and standby databases (or clusters) to avoid unexpected behavior after failover.  



###  Operation Steps

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Check the current configuration:

    ```sql
    SHOW PARAMETER RECYCLEBIN_ENABLED;

    NAME                                    VALUE
    --------------------------------------- ----------------------------------------
    RECYCLEBIN_ENABLED                      ON
    ```

    - `RECYCLEBIN_ENABLED = ON` indicates the recycle bin is enabled.

    - `RECYCLEBIN_ENABLED = OFF` indicates the recycle bin is disabled. Proceed to enable it if needed. 

3. Enable the recycle bin. The configuration takes effect immediately.  

    ```sql
    ALTER SYSTEM SET RECYCLEBIN_ENABLED='ON' scope=both;
    ```


4. Log in to other instances (or standby databases) and repeat the above steps to adjust configurations, ensuring global consistency.



##  Viewing Objects in the Recycle Bin

Use the [DBA_RECYCLEBIN view](../../../../Reference Manual/System Views/DBA Views/DBA_RECYCLEBIN) (or corresponding USER_RECYCLEBIN, ALL_RECYCLEBIN views) to inspect recycle bin contents.  

```sql
-- View all objects in the recycle bin  
SELECT OWNER,OBJECT_NAME,ORIGINAL_NAME,OPERATION,TYPE FROM DBA_RECYCLEBIN;
OWNER                     OBJECT_NAME               ORIGINAL_NAME             OPERATION                 TYPE
------------------------- ------------------------- ------------------------- ------------------------- ------------------
SYS                       BIN$3042                  EMPLOYEE_INFO             DROP                      TABLE
SALES                     BIN$3031                  FINANCE_INFO              DROP                      TABLE
SALES                     BIN$3032                  IDX_FINANCE_INFO_1        DROP                      INDEX

-- Query the total capacity of recycle bin data in each tablespace (in blocks)
SELECT ts_name, SUM(block_count) AS total_recyclebin_blocks FROM dba_recyclebin GROUP BY ts_name;
TS_NAME                                                          TOTAL_RECYCLEBIN_BLOCKS
---------------------------------------------------------------- -----------------------
USERS                                                                                 16
```

##  Purging the Recycle Bin

The objects in the recycle bin are still physically stored in their original tablespace, continue to occupy storage space, and their data will still be included in backups. These data can either be automatically processed by the database's cleanup mechanism or manually cleared on demand by executing the PURGE statement. For example:

- Perform timely/regular manual cleanup to free space when recycle bin objects and related data are no longer required.

- Cleanup unnecessary data in the recycle bin before [backing up](../../../Backup and Recovery/00Backup and Recovery) the database/tablespace to reduce backup size.

> **Caution**:
>
> - Once purged, objects or their dependent objects cannot be restored via flashback. For example, if an index is purged, restoring its parent table will recover the table and other dependent objects but not the purged index.
>
> - Purging a table automatically purges all its dependent objects. 

###  Automatic Cleanup

When a tablespace cannot allocate free space, the database attempts to purge recycle bin objects within that tablespace to free up storage.

###  Manual Cleanup

Use the PURGE statement to clean the recycle bin:

- Purge a specific object (table or index):

    - The objects in the recycle bin can be specified using their original name (the ORIGINAL_NAME field). If the same-named object (based on the ORIGINAL_NAME field) has entered the recycle bin multiple times (which will generate different OBJECT_NAME values), each cleanup operation will only remove one instance of the object in reverse chronological order.
        
        ```sql
        SELECT OBJECT_NAME,ORIGINAL_NAME,OPERATION,TYPE,RECYCLEBIN_TIME FROM DBA_RECYCLEBIN;

        OBJECT_NAME                                                      ORIGINAL_NAME                                                    OPERATION                 TYPE                                      RECYCLEBIN_TIME
        ---------------------------------------------------------------- ---------------------------------------------------------------- ------------------------- ----------------------------------------- -----------------------------------------
        BIN$3132                                                         FINANCE_INFO                                                     DROP                      TABLE                                     2025-09-02:15:59:03
        BIN$3133                                                         IDX_FINANCE_INFO_1                                               DROP                      INDEX                                     2025-09-02:15:59:03
        BIN$3134                                                         FINANCE_INFO                                                     DROP                      TABLE                                     2025-09-02:16:01:33
        BIN$3135                                                         IDX_FINANCE_INFO_1                                               DROP                      INDEX                                     2025-09-02:16:01:33

        -- Purge an index based on ORIGINAL_NAME
        PURGE INDEX IDX_FINANCE_INFO_1;

        -- Purge a table based on ORIGINAL_NAME
        PURGE TABLE FINANCE_INFO;

        SELECT OBJECT_NAME,ORIGINAL_NAME,OPERATION,TYPE,RECYCLEBIN_TIME FROM DBA_RECYCLEBIN;

        OBJECT_NAME                                                      ORIGINAL_NAME                                                    OPERATION                 TYPE                                      RECYCLEBIN_TIME
        ---------------------------------------------------------------- ---------------------------------------------------------------- ------------------------- ----------------------------------------- -----------------------------------------
        BIN$3134                                                         FINANCE_INFO                                                     DROP                      TABLE                                     2025-09-02:16:01:33
        BIN$3135                                                         IDX_FINANCE_INFO_1                                               DROP                      INDEX                                     2025-09-02:16:01:33
        ```
    
    - The objects in the recycle bin can be specified using the system-generated BIN$-prefixed name (the OBJECT_NAME field) when they enter the recycle bin.

        ```sql
        -- Purge an index based on OBJECT_NAME
        PURGE INDEX BIN$3135;

        SELECT OBJECT_NAME,ORIGINAL_NAME,OPERATION,TYPE,RECYCLEBIN_TIME FROM DBA_RECYCLEBIN;

        OBJECT_NAME                                                      ORIGINAL_NAME                                                    OPERATION                 TYPE                                      RECYCLEBIN_TIME
        ---------------------------------------------------------------- ---------------------------------------------------------------- ------------------------- ----------------------------------------- -----------------------------------------
        BIN$3134                                                         FINANCE_INFO                                                     DROP                      TABLE                                     2025-09-02:16:01:33
        ```


- Purge all objects of a user in a tablespace: 

    ```sql
    PURGE TABLESPACE yashan sales;
    ```

- Empty the recycle bin of a tablespace: 

    ```sql
    PURGE TABLESPACE yashan;
    ```

- Empty all recycle bins in the current database:

    ```sql
    PURGE DBA_RECYCLEBIN;
    ```

##  Disabling the Recycle Bin

When the recycle bin is disabled, deleting a HEAP table will permanently discard its data and release the occupied storage space immediately.  

###  Precautions



- In YAC/Distributed Cluster Deployment, recycle bin configurations across multiple instances will not be automatically synchronized. It is recommended to maintain consistent configurations; otherwise, only instances with the recycle bin enabled will move HEAP table data to the recycle bin when deletion operations are performed. To adjust recycle bin enable/disable settings, you must connect to each instance individually to perform the corresponding operations.

- In high availability (HA) deployments, the configuration is not synchronized between primary and standby databases (or clusters), and only the primary database's (or primary cluster's) configuration takes effect. Ensure consistent settings on primary and standby databases (or clusters) to avoid unexpected behavior after failover.  



###  Operation Steps

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Check the current configuration:

    ```sql
    SHOW PARAMETER RECYCLEBIN_ENABLED;

    NAME                                    VALUE
    --------------------------------------- ----------------------------------------
    RECYCLEBIN_ENABLED                      ON
    ```

    - `RECYCLEBIN_ENABLED = ON` indicates the recycle bin is enabled. Proceed to disable it if needed. 

    - `RECYCLEBIN_ENABLED = OFF` indicates the recycle bin is disabled.

3. Disable the recycle bin. The configuration takes effect immediately. 

    ```sql
    ALTER SYSTEM SET RECYCLEBIN_ENABLED='OFF'  scope=both;
    ```



4. Log in to other instances (or standby databases) and repeat the above steps to adjust configurations, ensuring global consistency.

