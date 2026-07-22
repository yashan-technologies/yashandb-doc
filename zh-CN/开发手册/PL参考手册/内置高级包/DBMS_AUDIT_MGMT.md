DBMS_AUDIT_MGMT包提供了一组内置的存储过程，用于创建和管理审计清理任务。需要注意的是，所有相关程序的参数值必须按序输入。

仅允许sys用户、AUDIT_ADMIN角色以及DBA角色的用户调用该高级包。


## SET_LAST_ARCHIVE_TIMESTAMP

```plsql
 DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN INTEGER,
    last_archive_time IN TIMESTAMP);
```

SET_LAST_ARCHIVE_TIMESTAMP程序用于设置清理时间点，成功设置的清理时间点可以在DBA_AUDIT_MGMT_LAST_ARCH_TS视图中查询。

|  参数| 描述|
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_type           | 审计清理类型，当前只支持DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED。   |
| last_archive_time          | 清理时间点，不能超过当前时间。   |

示例

```plsql
-- 设置清理时间点
BEGIN
    DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(
        DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED ,
        sysdate);
END;
/
```

## CLEAN_AUDIT_TRAIL

```plsql
DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL(
    audit_trail_type IN INTEGER,
    use_last_arch_timestamp IN BOOLEAN DEFAULT TRUE);
```

CLEAN_AUDIT_TRAIL程序用于进行审计日志内容清理。
|  参数| 描述|
| :---------------------- | :----------------------------------------------------------- |
| audit_trail_type        | 审计清理类型，当前只支持DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED。 |
| use_last_arch_timestamp | 是否使用清理时间，值为TRUE或FALSE。若为TRUE则只删除小于清理时间点的审计数据，若为FALSE则删除所有审计数据。 |

示例

```plsql
-- 执行审计日志清理，清除审计清理时间点之前的审计数据。
BEGIN
  DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL (
        DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED,
        true);
END;
/
```

## CREATE_PURGE_JOB

```plsql
DBMS_AUDIT_MGMT.CREATE_PURGE_JOB(
    audit_trail_type IN INTEGER,
    audit_trail_start_time IN timestamp,
    audit_trail_purge_interval IN varchar,
    audit_trail_purge_name IN VARCHAR,
    use_last_arch_timestamp IN BOOLEAN DEFAULT TRUE);
```

CREATE_PURGE_JOB程序用于创建一个审计清理定时任务，成功创建的定时任务将可以在DBA_AUDIT_MGMT_CLEANUP_JOBS视图中查询，审计清理定时任务创建时默认状态为开启。

|  参数| 描述|
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_type           | 审计清理类型，当前只支持DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED。 |
| audit_trail_start_time     | 定时任务开始时间。                                           |
| audit_trail_purge_interval | 表达式文本，用于计算定时任务下次执行的时间。                 |
| audit_trail_purge_name     | 定时任务名称。                                               |
| use_last_arch_timestamp    | 是否使用清理时间，值为TRUE或FALSE。若为TRUE则只有小于清理时间点的数据被删除，若为FALSE则会清理所有的审计记录。 |

**定期更新审计清理时间点**

使用审计清理定时任务处理审计数据清理时，默认将清理时间点之前的审计数据做清理。在数据库使用期间，审计日志不断积累，需要根据实际需要定期更新清理时间点。

可以通过YashanDB提供的[DBMS_SCHEDULER](./DBMS_SCHEDULER).CREATE_JOB进行定期更新审计清理时间点，所创建的任务计划可在DBA_SCHEDULER_JOBS中查询。

示例

```plsql
-- 将审计清理时间点设置为30天前，即只保留最近30天的审计日志，每天执行一次刷新
BEGIN
DBMS_SCHEDULER.CREATE_JOB (
     'update_audit_archive_time',
    'PLSQL_BLOCK',
    'BEGIN DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED, sysdate-30);END;' ,
    0,
    sysdate,
    'sysdate+1',
    NULL,
    'DEFAULT_JOB_CLASS',
    TRUE,
    FALSE,
    'update audit archive time');
END;
/    
```

**创建审计清理定时任务**

创建审计清理时间点定时任务后，创建审计清理定时任务。

示例

```plsql
-- 创建自动清理任务，5个小时后开始第一次执行，每次执行时间间隔为1天
BEGIN
  DBMS_AUDIT_MGMT.CREATE_PURGE_JOB (
      DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED,
      sysdate + 5/24,
      'sysdate + 1',
      'audit_job',
     TRUE);
END;
/
```

## SET_PURGE_JOB_STATUS

```plsql
DBMS_AUDIT_MGMT.SET_PURGE_JOB_STATUS(
    audit_trail_purge_name IN VARCHAR,
    audit_trail_status_value IN INTEGER);
```

SET_PURGE_JOB_STATUS程序用于设置定时任务的启停标志，被停止的定时任务将不再被系统进行任务调度。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| audit_trail_purge_name       | 定时任务名称，可以通过DBA_AUDIT_MGMT_CLEANUP_JOBS视图查询。 |
| audit_trail_status_value    | 是否停止定时任务，DBMS_AUDIT_MGMT.PURGE_JOB_ENABLE表示启动，DBMS_AUDIT_MGMT.PURGE_JOB_DISABLE表示停止。|

示例

```plsql
-- 停止审计清理定时任务
BEGIN
  DBMS_AUDIT_MGMT.SET_PURGE_JOB_STATUS (
        'audit_job',
        DBMS_AUDIT_MGMT.PURGE_JOB_DISABLE);
END;
/
```

## SET_PURGE_JOB_INTERVAL

示例

```plsql
DBMS_AUDIT_MGMT.SET_PURGE_JOB_INTERVAL(
    audit_trail_purge_name IN VARCHAR,
    audit_trail_interval_value IN VARCHAR);
```

SET_PURGE_JOB_INTERVAL程序用于修改审计日志定时任务下次执行时间。

|  参数| 描述|
| :--------------------------- | :----------------------------------------------------------- |
| audit_trail_purge_name       | 定时任务名称，可以通过DBA_AUDIT_MGMT_CLEANUP_JOBS视图查询。 |
| audit_trail_interval_value      | 表达式文本，用于计算定时任务下次执行的时间。 |

示例

```plsql
-- 修改审计清理定时任务的执行间隔为7天
BEGIN
DBMS_AUDIT_MGMT.SET_PURGE_JOB_INTERVAL(
     'audit_job',
     'sysdate + 7');
END;
/
```

## GET_LAST_ARCHIVE_TIMESTAMP

```plsql
DBMS_AUDIT_MGMT.GET_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN PLS_INTEGER)
RETURN TIMESTAMP;
```

GET_LAST_ARCHIVE_TIMESTAMP程序获取审计清理时间点。

|  参数| 描述|
| :-------------------- | :----------------------------------------------------------- |
| audit_trail_type      | 审计清理类型，当前只支持DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED。 |

示例

```plsql
-- 获取审计清理时间点
DECLARE
 last_time TIMESTAMP;
BEGIN
 last_time := DBMS_AUDIT_MGMT.GET_LAST_ARCHIVE_TIMESTAMP(DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED);
 IF last_time is not NULL THEN
  DBMS_OUTPUT.PUT_LINE('last archive timestamp is: ' || to_char(last_time));
 else
   DBMS_OUTPUT.PUT_LINE('last archive timestamp is null');
 END IF;
END;
/

--result
last archive timestamp is: 2022-11-05 17:12:34.000000
```

## CLEAR_LAST_ARCHIVE_TIMESTAMP

```plsql
DBMS_AUDIT_MGMT.CLEAR_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN PLS_INTEGER);
```

CLEAR_LAST_ARCHIVE_TIMESTAMP程序用于清空审计清理时间点。

|  参数| 描述|
| :-------------------- | :----------------------------------------------------------- |
| audit_trail_type      | 审计清理类型，当前只支持DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED。 |

示例

```plsql
-- 清空审计清理时间点
BEGIN
    DBMS_AUDIT_MGMT.CLEAR_LAST_ARCHIVE_TIMESTAMP
    (DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED);
END;
/
```

## DROP_PURGE_JOB

```plsql
DBMS_AUDIT_MGMT.DROP_PURGE_JOB (
    audit_trail_purge_name IN VARCHAR);
```

DROP_PURGE_JOB程序用于删除清理定时任务。

|  参数| 描述|
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_purge_name     | 定时任务名称。                                               |

## 异常说明

调用DBMS_AUDIT_MGMT高级包进行审计清理相关处理时，若参数设置不正确会抛出如下异常：

|  错误码| 错误内容| 解释|
| :------------- | :------------------------------------------------------------ | :--------------------------------------- |
| YAS-30000      | invalid value of argument AUDIT_TRAIL_TYPE                    | 审计清理类型的设置非法                     |
| YAS-30001      | invalid value of argument AUDIT_TRAIL_STATUS_VALUE            | 审计清理job状态的设置非法                  |
| YAS-30002      | invalid value of argument LAST_ARCHIVE_TIME                   | 审计清理时间点的设置非法                   |
| YAS-30003      | invalid value of argument AUDIT_TRAIL_PURGE_INTERVAL          | 下次执行时间的设置非法                     |
| YAS-30004      | Cleanup job already existed for the given audit trail type    | 给定类型的审计清理job已存在                |
