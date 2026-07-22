The DBMS_WORKLOAD_REPOSITORY advanced package provides interfaces for interacting with ASH functionality, including report generation and ASH record persistence.

## ASH_REPORT_HTML

```plsql
DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
  dbid              IN NUMBER DEFAULT NULL,
  instance_number   IN NUMBER DEFAULT NULL,
  start_time        IN TIMESTAMP DEFAULT NULL,
  end_time          IN TIMESTAMP DEFAULT NULL,
  data_src          IN NUMBER DEFAULT 0);
```

The ASH_REPORT_HTML procedure is used to generate a History active sessions report in HTML format.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| dbid         | Database ID, default NULL indicates the current database ID |
| instance_number         | Instance ID, default NULL indicates the current instance ID |
| start_time         | Report start time, default NULL indicates 15 minutes ago |
| end_time         | Report end time, default NULL indicates the current time |
| data_src         | Report data source, 1 indicates from memory, 2 indicates from disk, 0 indicates from both        |

***Example***

```sql
-- Generate History active sessions HTML report for the current instance for the past 1 hour
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
        start_time => SYSDATE - 1/24,
        end_time => SYSDATE,
        data_src => 0
    );

-- Generate an HTML report of the ASH for the current instance within a specified time period
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(
    dbid => 3743563942,
    instance_number => 1,
    start_time => to_timestamp('2026-03-05 10:10:10', 'yyyy-mm-dd hh24:mi:ss'),
    end_time => to_timestamp('2026-03-05 23:30:10', 'yyyy-mm-dd hh24:mi:ss'),
    -- 0: memory and disk, 1: memory only, 2: disk only
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

The ASH_REPORT_TEXT procedure is used to generate a History active sessions report in text format.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| dbid         | Database ID, default NULL indicates the current database ID |
| instance_number         | Instance ID, default NULL indicates the current instance ID |
| start_time         | Report start time, default NULL indicates 15 minutes ago |
| end_time         | Report end time, default NULL indicates the current time |
| data_src         | Report data source, 1 indicates from memory, 2 indicates from disk, 0 indicates from both        |

**Example**

```sql
-- Generate ASH text report for a specific instance for the past 30 minutes
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_TEXT(
        dbid => 1234567890,
        instance_number => 1,
        start_time => SYSDATE - 30/1440,
        end_time => SYSDATE,
        data_src => 0
    );

-- Generate a text report of the ASH for a specified instance within a specified time period
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

The DBMS_WORKLOAD_REPOSITORY.ASH_FLUSH subprogram is used to flush ASH records to disk.

> **Caution**:
>
> The ash_flush subprogram can only be called by the DBMS_WORKLOAD_REPOSITORY advanced package and is not allowed to be called directly by users.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| l_snap_id         | Snapshot ID |
| l_begin_flush_id         | Smaple ID to begin flushing |

