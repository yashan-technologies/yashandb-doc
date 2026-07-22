DBMS_WORKLOAD_REPOSITORY高级包提供了与ASH功能交互的接口，包括生成相关报告功能以及ASH记录持久化功能。

## ASH_REPORT_HTML

```plsql
DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
  dbid              IN NUMBER DEFAULT NULL,
  instance_number   IN NUMBER DEFAULT NULL,
  start_time        IN TIMESTAMP DEFAULT NULL,
  end_time          IN TIMESTAMP DEFAULT NULL,
  data_src          IN NUMBER DEFAULT 0);
```

ASH_REPORT_HTML过程用于生成HTML格式的活跃会话历史报告。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| dbid | 数据库ID，默认NULL表示当前的数据库ID      |
| instance_number | 实例ID，默认NULL表示当前实例ID       |
| start_time | 报告起始时间，默认NULL表示15分钟前       |
| end_time | 报告结束时间，默认NULL表示当前时间       |
| data_src | 报告数据源，1表示来源于内存，2表示来源于磁盘，0表示来源于两者       |

示例

```sql
-- 生成当前实例过去1小时的活跃会话历史的HTML报告
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
        start_time => SYSDATE - 1/24,
        end_time => SYSDATE,
        data_src => 0
    );

-- 生成当前实例指定期间内的ASH的HTML报告
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
    dbid => 3743563942,
    instance_number => 1,
    start_time => to_timestamp('2026-03-05 10:10:10', 'yyyy-mm-dd hh24:mi:ss'),
    end_time => to_timestamp('2026-03-05 23:30:10', 'yyyy-mm-dd hh24:mi:ss'),
    -- 0:内存和磁盘，1:仅内存，2:仅磁盘
    data_src => 0 
);
```

## ASH_REPORT_TEXT

```plsql
DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_TEXT(
  dbid              IN NUMBER DEFAULT NULL,
  instance_number   IN NUMBER DEFAULT NULL,
  start_time        IN TIMESTAMP DEFAULT NULL,
  end_time          IN TIMESTAMP DEFAULT NULL,
  data_src          IN NUMBER DEFAULT 0);
```

ASH_REPORT_TEXT过程用于生成文本格式的活跃会话历史报告。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| dbid | 数据库ID，默认NULL表示当前的数据库ID      |
| instance_number | 实例ID，默认NULL表示当前实例ID       |
| start_time | 报告起始时间，默认NULL表示15分钟前       |
| end_time | 报告结束时间，默认NULL表示当前时间       |
| data_src | 报告数据源，1表示来源于内存，2表示来源于磁盘，0表示来源于两者       |

示例

```sql
-- 生成指定实例过去30分钟的ASH文本报告
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_TEXT(
        dbid => 1234567890,
        instance_number => 1,
        start_time => SYSDATE - 30/1440,
        end_time => SYSDATE,
        data_src => 0
    );

-- 生成指定实例指定时间期间的ASH文本报告
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_TEXT(
    dbid => 3743563942,
    instance_number => 1,
    start_time => to_timestamp('2026-03-05 10:10:10', 'yyyy-mm-dd hh24:mi:ss'),
    end_time => to_timestamp('2026-03-05 23:30:10', 'yyyy-mm-dd hh24:mi:ss'),
    data_src => 0
);
```

## ASH_FLUSH

```plsql
DBMS_WORKLOAD_REPOSITORY.ASH_FLUSH(l_snap_id IN NUMBER, l_begin_flush_id IN NUMBER)
```

DBMS_WORKLOAD_REPOSITORY.ASH_FLUSH子程序用于进行ASH记录刷盘。

> **Caution**:
>
> 该ash_flush子程序只能通过DBMS_WORKLOAD_REPOSITORY高级包调用，不允许用户直接调用。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| l_snap_id | 快照ID      |
| l_begin_flush_id | 开始刷盘的样本ID      |

