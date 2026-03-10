## Enabling Audit Functionality

This functionality is controlled by the value of the UNIFIED_AUDITING parameter. After installation, YashanDB defaults UNIFIED_AUDITING = FALSE, which means auditing functionality is turned off, and no auditing will take place in this state.

> **Note**: 
>
>  Standby databases will never perform auditing regardless of the UNIFIED_AUDITING parameter value.


1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute the ALTER SYSTEM SET PARAMETER statement to modify the UNIFIED_AUDITING parameter value.

    ```sql
    ALTER SYSTEM SET UNIFIED_AUDITING=TRUE;

    -- For ISC Distributed Cluster Deployment, TYPE must also be specified, with values CN|DN|MN|ALL
    ALTER SYSTEM SET UNIFIED_AUDITING=TRUE TYPE=ALL;
    ```
    After the audit functionality is enabled, the system will execute the enabled [audit policies](Audit Policy Management).

##  Configuring Audit Log Storage

In Standalone/YAC/Distributed Cluster Deployment, audit logs can be stored in system tables (default) or external files. Configure scheduled tasks to enable automatic archiving of audit logs, preventing excessive growth from impacting database auditing.  

In ISC Distributed Cluster Deployment, audit logs can **only** be stored in system tables. No adjustments or switching are allowed, and this operation is not required.

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Check if audit log storage parameters meet expectations. Adjust them if necessary.

    ```sql
    -- View parameters
    SELECT NAME,VALUE,DEFAULT_VALUE FROM V$PARAMETER WHERE NAME LIKE 'AUDIT_%';

    NAME                                        VALUE                                       DEFAULT_VALUE
    ------------------------------------------  ------------------------------------------  ------------------------------------------
    AUDIT_QUEUE_WRITE                           TRUE                                        TRUE
    AUDIT_QUEUE_SIZE                            16M                                         16M
    AUDIT_FLUSH_INTERVAL                        100                                         100
    AUDIT_RECORD_THRESHOLD                      50                                          50
    AUDIT_RECORD_TABLE                          TRUE                                        TRUE
    AUDIT_LOG_FILE_COUNT                        200                                         200
    AUDIT_LOG_FILE_PATH                         ?/log/audit                                 ?/log/audit
    AUDIT_LOG_FILE_SIZE                         256M                                        256M

    -- Adjust parameters as needed (e.g., limit audit log files to 100)
    ALTER SYSTEM SET AUDIT_LOG_FILE_COUNT = 100 SCOPE=BOTH;
    ```

    | Category | Parameter | Description |
    |----------|----------|--------------------------------------|
    | Asynchronous Auditing | AUDIT_QUEUE_WRITE | Enables asynchronous audit logging. Values: [TRUE&#124;FALSE]. Default: TRUE. |
    |  | AUDIT_QUEUE_SIZE | The size of the audit queue buffer, which is the amount of memory space available for writing audit logs asynchronously. It only comes into effect when AUDIT_QUEUE_WRITE = TRUE, and the value range is [4M, 32M]. |
    |  | AUDIT_FLUSH_INTERVAL | Interval for flushing audit logs (milliseconds). Active when AUDIT_QUEUE_WRITE=TRUE. Range: [50,5000]. |  
    | Storage Mode | AUDIT_RECORD_TABLE | Whether audit logs are stored in system tables. Values: [TRUE&#124;FALSE]. Default: TRUE. |
    | System Table Config | AUDIT_RECORD_THRESHOLD | Warning threshold for SYSAUX tablespace usage by audit system tables (%). Range: [1,70].<br/>Exceeding this value logs an "AUDIT RECORD THRESHOLD" entry. Storage mode switches only when usage exceeds 80% (fixed). |
    | File Config | AUDIT_LOG_FILE_PATH | Audit log file storage path (default: `$YASDB_DATA/log/audit`).<br/>Filename format: `audit-yyyymmddhhmmss.aud` (immutable). |
    |  | AUDIT_LOG_FILE_SIZE | Max size per audit log file. Range: [128M,4G].<br/>Exceeding this triggers archiving and creates a new file. |
    |  | AUDIT_LOG_FILE_COUNT | Max number of audit log files. Range: [2,10000].<br/>Exceeding this deletes the oldest file. |

3. Use the [DBMS_SCHEDULER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_SCHEDULER) package to create scheduled tasks for automatic log archiving.

    ```plsql
    -- Example: Execute CHECK_AUDIT_THRESHOLD every 6 seconds
    exec dbms_scheduler.create_job('check_audit_threshold',
        'PLSQL_BLOCK',
        'declare v1 int; begin select check_audit_threshold() into v1 from dual; end;',
        0,
        sysdate,
        'sysdate + 1/(24*60*10)',
        '2025-12-31 12:00:00',
        'DEFAULT_JOB_CLASS',
        true,
        false,
        null);
    ```

    > **Note**:
    > 
    > Audit logs stored in external files are **not visible** in audit views. Manually clean system tables (or create cleanup tasks) to reduce SYSAUX usage and switch back to table storage.
