```ebnf+diagram
check_audit_threshold::= CHECK_AUDIT_THRESHOLD "("")"
```

The CHECK_AUDIT_THRESHOLD function checks the percentage of the SYSAUX tablespace occupied by audit log system table and returns a NUMBER value.

This function follows the rules below:

- This function is not applicable to ISC Distributed Cluster Deployment.

- This function is meaningful only when auditing is enabled (`UNIFIED_AUDITING = TRUE`). 

- Real-time results from this function may have deviations. For the most accurate results, refer to [statistics](../../../性能调优/性能调优特性与工具/统计信息).  

After execution, the system automatically triggers actions based on results:

- If the return value > specified threshold \([AUDIT_RECORD_THRESHOLD](../../../参考手册/配置参数.html#auditthreshold)\), an audit log entry with action "AUDIT RECORD THRESHOLD" is recorded.

- If the return value > 80, record "AuditRecordableFull" in alert.rog, check the value of [AUDIT_RECORD_TABLE](../../../参考手册/配置参数.html#auditdest): 

    - record alarm content "AuditRecordableFull" in alert.rog.

    - If `AUDIT_RECORD_TABLE = TRUE`, switch storage mode to external files (`AUDIT_RECORD_TABLE = FALSE`). Subsequent logs are written to files, and an "AUDIT RECORD SWITCH" entry is logged.
    
    - If `AUDIT_RECORD_TABLE = FALSE`, no action is taken.

- If the return value ≤ 80, check the value of AUDIT_RECORD_TABLE: 
    
    - If `AUDIT_RECORD_TABLE = FALSE`, switch storage mode to system tables (`AUDIT_RECORD_TABLE = TRUE`). Subsequent logs are written to tables, and an "AUDIT RECORD SWITCH" entry is logged.

    - If `AUDIT_RECORD_TABLE = TRUE`, no action is taken. 


***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Verify auditing is enabled
SHOW PARAMETER UNIFIED_AUDITING;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
UNIFIED_AUDITING                                                 TRUE

-- Check current configuration of AUDIT_RECORD_TABLE
SHOW PARAMETER AUDIT_RECORD_TABLE;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
AUDIT_RECORD_TABLE                                               FALSE

-- Manually clean audit logs
BEGIN
    DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL(
    AUDIT_TRAIL_TYPE => DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED ,
    USE_LAST_ARCH_TIMESTAMP =>  false );
END;
/

-- Execute CHECK_AUDIT_THRESHOLD 
SELECT CHECK_AUDIT_THRESHOLD() RES FROM DUAL;
         RES
------------
          0

-- If return value ≤ 80, check AUDIT_RECORD_TABLE again 
SHOW PARAMETER AUDIT_RECORD_TABLE;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
AUDIT_RECORD_TABLE                                               TRUE

-- View audit logs
SELECT EVENT_TIMESTAMP,ACTION,SQL_TEXT FROM UNIFIED_AUDIT_TRAIL WHERE ACTION = 'AUDIT RECORD SWITCH';

EVENT_TIMESTAMP                                                  ACTION                                                           SQL_TEXT
---------------------------------------------------------------- ---------------------------------------------------------------- ----------------------------------------------------------------
2025-08-13 19:42:01.396900                                       AUDIT RECORD SWITCH                                              AUDIT RECORD SWITCH
```
