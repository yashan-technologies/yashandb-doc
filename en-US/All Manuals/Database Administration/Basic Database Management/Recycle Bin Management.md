The database Recycle Bin functions similarly to an operating system's recycle bin, temporarily storing deleted database objects such as tables and indexes. It prevents permanent data loss when objects are deleted, allowing quick and efficient recovery of accidentally deleted objects until the Recycle Bin is purged.

The recycle bin is only applicable to HEAP tables. Deleting TAC tables, LSC tables, and external tables will always result in permanent deletion.

The recycle bin depends on the tablespace. When the recycle bin is enabled, deleting a HEAP table in a tablespace without specifying the PURGE keyword causes the system to place the table object and its data into the recycle bin of that tablespace (the table name is renamed to a BIN$-prefixed system-generated string).

##  Enabling the Recycle Bin

The Recycle Bin is controlled by the configuration parameter RECYCLEBIN_ENABLED, which defaults to ON (enabled).

###  Precautions

- During configuration changes, avoid concurrent DROP/TRUNCATE operations, as the Recycle Bin may not yet be fully enabled, leading to permanent object deletion.



- In YAC Deployment, recycle bin configurations across multiple instances will not be automatically synchronized. It is recommended to maintain consistent configurations; otherwise, only instances with the recycle bin enabled will move HEAP table data to the recycle bin when deletion operations are performed. To adjust recycle bin enable/disable settings, you must connect to each instance individually to perform the corresponding operations.

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

    - `RECYCLEBIN_ENABLED = ON` indicates the Recycle Bin is enabled.

    - `RECYCLEBIN_ENABLED = OFF` indicates the Recycle Bin is disabled. Proceed to enable it if needed. 

3. Enable the Recycle Bin. The configuration takes effect immediately.  

    ```sql
    ALTER SYSTEM SET RECYCLEBIN_ENABLED='ON' scope=both;
    ```


4. Log in to other instances (or standby databases) and repeat the above steps to adjust configurations, ensuring global consistency.



##  Managing Recycle Bin Objects

###  Supported Objects

When the Recycle Bin is enabled, the following objects can be moved to the Recycle Bin: HEAP tables, their dependent objects, and associated data. 

The behavior of deletion operations is described in the table below. 

|Target Object     | Operation      | Behavior    |
|--------------------|-----------------|---------------------|
| HEAP tables and their dependent objects in the SYSTEM tablespace | -          | Never enter the Recycle Bin.               |  
| HEAP tables in other tablespaces    | DROP TABLE (without PURGE)    | The table, its partitions, indexes, and triggers based on the table are moved to the Recycle Bin. |  
|   | TRUNCATE TABLE                  | The table, its partitions, indexes, and triggers based on the table are moved to the Recycle Bin. |
|   | ALTER TABLE TRUNCATE PARTITION  | - If the target partition resides in the SYSTEM tablespace: Does not enter the Recycle Bin.<br/>- If in other tablespaces: Moves to the Recycle Bin. |  

> **Note**:
>
> - Dropping dependent objects of a HEAP table (e.g., indexes, triggers) via the DROP statement will not move them to the Recycle Bin.
> 
> - If the tablespace is full or near capacity when performing these operations, objects and data may be purged from the Recycle Bin immediately.

###  Viewing Objects in the Recycle Bin

Use the [DBA_RECYCLEBIN view](../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) (or corresponding USER_RECYCLEBIN, ALL_RECYCLEBIN views) to inspect Recycle Bin contents.  

```sql
-- View all objects in the recycle bin  
SELECT OWNER,OBJECT_NAME,ORIGINAL_NAME,OPERATION,TYPE FROM DBA_RECYCLEBIN;
OWNER                     OBJECT_NAME               ORIGINAL_NAME             OPERATION                 TYPE
------------------------- ------------------------- ------------------------- ------------------------- ------------------
SYS                       BIN$3042                  EMPLOYEE_INFO             DROP                      TABLE
SALES                     BIN$3031                  FINANCE_INFO              DROP                      TABLE
SALES                     BIN$3032                  IDX_FINANCE_INFO_1        DROP                      INDEX

-- Query the total capacity of recycle bin data in each tablespace (in blocks)
SELECT ts_name, SUM(block_count) AS total_recyclebin_blocks FROM DBA_RECYCLEBIN GROUP BY ts_name;
TS_NAME                                                          TOTAL_RECYCLEBIN_BLOCKS
---------------------------------------------------------------- -----------------------
USERS                                                                                 16
```

###  Restoring Objects from the Recycle Bin

Use the FLASHBACK statement to restore tables/table partitions from the Recycle Bin. For detailed operations, refer to [Table Flashback](../闪回/表闪回).  



When flashing back tables/partitions, data recovery follows these rules: 

- If the object to be recovered is a table that has been dropped, you need to check whether the original table name is occupied. If it is, you must rename the table during the Flashback operation; otherwise, an error will occur.

- If an object has been dropped multiple times, each Flashback operation will only restore one DROP operation in reverse chronological order. That is, the first Flashback will restore only the most recent DROP. If the previous Flashback operation did not rename the object (retaining the original table name), subsequent Flashback operations will no longer be able to restore the object using the original name.

- If an object has been truncated multiple times, each Flashback operation will only restore one TRUNCATE operation in reverse chronological order. That is, the first Flashback will restore only the most recent TRUNCATE operation.

- Objects always adhere to hierarchical dependencies:

    - Partitions that entered the Recycle Bin via TRUNCATE TABLE cannot be restored individually. Subpartitions that entered via TRUNCATE PARTITION of a primary partition cannot be restored individually.

    - Dependent objects (indexes, triggers, etc.) cannot be restored separately. They are automatically recovered with their parent table/partition if still present in the Recycle Bin.



```sql
-- Restore finance_info table (retains original name)
FLASHBACK TABLE finance_info TO BEFORE DROP;
-- or
FLASHBACK TABLE BIN$3031 TO BEFORE DROP;

-- Restore the employees table and rename it to employees_recycle
FLASHBACK TABLE employees TO BEFORE DROP RENAME TO employees_recycle;

-- Restore table data after TRUNCATE
FLASHBACK TABLE product TO BEFORE TRUNCATE;
```

##  Purging the Recycle Bin

The objects in the recycle bin are still physically stored in their original tablespace, continue to occupy storage space, and their data will still be included in backups. These data can either be automatically processed by the database's cleanup mechanism or manually cleared on demand by executing the PURGE statement. For example:

- Perform timely/regular manual cleanup to free space when Recycle Bin objects and related data are no longer required.

- Cleanup unnecessary data in the Recycle Bin before [backing up](../备份恢复/00备份恢复) the database/tablespace to reduce backup size.

> **Caution**:
>
> - Once purged, objects or their dependent objects cannot be restored via flashback. For example, if an index is purged, restoring its parent table will recover the table and other dependent objects but not the purged index.
>
> - Purging a table automatically purges all its dependent objects. 

###  Automatic Cleanup

When a tablespace cannot allocate free space, the database attempts to purge Recycle Bin objects within that tablespace to free up storage.

###  Manual Cleanup

Use the PURGE statement to clean the Recycle Bin:

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

- Empty the Recycle Bin of a tablespace: 

    ```sql
    PURGE TABLESPACE yashan;
    ```

- Empty all Recycle Bins in the current database:

    ```sql
    PURGE DBA_RECYCLEBIN;
    ```

##  Disabling the Recycle Bin

When the Recycle Bin is disabled, deleting a HEAP table will permanently discard its data and release the occupied storage space immediately.  

###  Precautions



- In YAC Deployment, recycle bin configurations across multiple instances will not be automatically synchronized. It is recommended to maintain consistent configurations; otherwise, only instances with the recycle bin enabled will move HEAP table data to the recycle bin when deletion operations are performed. To adjust recycle bin enable/disable settings, you must connect to each instance individually to perform the corresponding operations.

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

    - `RECYCLEBIN_ENABLED = ON` indicates the Recycle Bin is enabled. Proceed to disable it if needed. 

    - `RECYCLEBIN_ENABLED = OFF` indicates the Recycle Bin is disabled.

3. Disable the Recycle Bin. The configuration takes effect immediately. 

    ```sql
    ALTER SYSTEM SET RECYCLEBIN_ENABLED='OFF'  scope=both;
    ```



4. Log in to other instances (or standby databases) and repeat the above steps to adjust configurations, ensuring global consistency.

