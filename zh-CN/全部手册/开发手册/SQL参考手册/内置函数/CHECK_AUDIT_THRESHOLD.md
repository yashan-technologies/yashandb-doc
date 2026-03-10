```ebnf+diagram
check_audit_threshold::= CHECK_AUDIT_THRESHOLD "("")"
```

CHECK_AUDIT_THRESHOLD函数用于检测审计日志的系统表占用SYSAUX表空间容量的百分比，返回值为NUMBER类型。

本函数遵循如下规则：

- 不适用于存算一体分布式集群部署。

- 本函数在开启审计功能（UNIFIED_AUDITING = TRUE）后执行才有意义。

- 执行本函数所得实时结果可能存在偏差，最精准的结果应以[统计信息](../../../性能调优/性能调优特性与工具/统计信息)为准。

执行本函数后，系统会根据函数结果自动触发以下操作：

- 返回值 > 给定阈值（[AUDIT_RECORD_THRESHOLD](../../../参考手册/配置参数.html#auditthreshold)参数），记录1条ACTION为“AUDIT RECORD THRESHOLD”的审计日志。

- 返回值 > 80，在告警日志alert.log中记录“AuditRecordTableFull”并判断[AUDIT_RECORD_TABLE](../../../参考手册/配置参数.html#auditdest)参数的值：

    - 写告警内容"AuditRecordTableFull"到告警日志alert.log中。

    - 若AUDIT_RECORD_TABLE = TRUE，自动切换审计日志的存储方式（将AUDIT_RECORD_TABLE改为FALSE），后续的审计日志记录写入外部文件，并在文件中记录1条ACTION为“AUDIT RECORD SWITCH”的审计日志。
    
    - 若AUDIT_RECORD_TABLE = FALSE，无操作。

- 返回值 ≤ 80，判断AUDIT_RECORD_TABLE参数的值：
    
    - 若AUDIT_RECORD_TABLE = FALSE，自动切换审计日志的存储方式（将AUDIT_RECORD_TABLE改为TRUE），后续的审计日志记录写入系统表，并在相应系统表中记录1条ACTION为“AUDIT RECORD SWITCH”的审计日志。

    - 若AUDIT_RECORD_TABLE = TRUE，无操作。


示例（单机/共享集群/分布式集群部署）

```plsql
-- 确认已开启审计功能
SHOW PARAMETER UNIFIED_AUDITING;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
UNIFIED_AUDITING                                                 TRUE

-- 查看AUDIT_RECORD_TABLE的当前配置
SHOW PARAMETER AUDIT_RECORD_TABLE;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
AUDIT_RECORD_TABLE                                               FALSE

-- 手动清理审计日志
BEGIN
    DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL(
    AUDIT_TRAIL_TYPE => DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED ,
    USE_LAST_ARCH_TIMESTAMP =>  false );
END;
/

-- 执行CHECK_AUDIT_THRESHOLD函数
SELECT CHECK_AUDIT_THRESHOLD() RES FROM DUAL;
         RES
------------
          0

-- 返回值不超过80，再次查看AUDIT_RECORD_TABLE的配置
SHOW PARAMETER AUDIT_RECORD_TABLE;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
AUDIT_RECORD_TABLE                                               TRUE

-- 查看审计日志
SELECT EVENT_TIMESTAMP,ACTION,SQL_TEXT FROM UNIFIED_AUDIT_TRAIL WHERE ACTION = 'AUDIT RECORD SWITCH';

EVENT_TIMESTAMP                                                  ACTION                                                           SQL_TEXT
---------------------------------------------------------------- ---------------------------------------------------------------- ----------------------------------------------------------------
2025-08-13 19:42:01.396900                                       AUDIT RECORD SWITCH                                              AUDIT RECORD SWITCH
```
