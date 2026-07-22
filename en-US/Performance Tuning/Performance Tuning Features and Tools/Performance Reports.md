YashanDB periodically saves a snapshot of the database status data into the WRM$_SNAPSHOT table during its operation. The status data includes wait events, metric data, space usage statistics, SQL state information, and other critical database statistics. Performance reports reflect the entire database status over a period by comparing two historical snapshots, providing a basis for post-event analysis and diagnosis of database anomalies.

## Snapshot

By default, snapshots are created by the system at regular intervals (default is every hour; this value can be modified using [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).MODIFY_SNAPSHOT_SETTINGS).

To diagnose performance bottlenecks, users may need to create a snapshot instantaneously at a specified point in time (e.g., before and after executing a SQL statement) to generate a performance report. At this point, a snapshot can be manually created by calling the [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).CREATE_SNAPSHOT procedure.

Snapshot data will be automatically cleaned by the system after a certain period (default is 8 days; this value can also be modified using [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).MODIFY_SNAPSHOT_SETTINGS), or manually cleaned via [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).DROP_SNAPSHOT_RANGE.

**Main Information of Snapshot**

Contents of the WRM$_SNAPSHOT table:

|Field Name |Meaning |
| ------------------- | ------------------------------------------------------------ |
| SNAP_ID             | Snapshot ID, identifier for each snapshot, required as a key parameter for report generation |
| DBID                | Database ID, same as database_id in V$DATABASE                 |
| INSTANCE_NUMBER     | Instance identifier, same as instance_number in V$INSTANCE     |
| STARTUP_TIME        | Instance startup time; reports generated from two consecutive snapshots are meaningful only if the database has been running continuously. This information helps filter and judge the validity of snapshot IDs during report generation |
| BEGIN_INTERVAL_TIME | Start time of the snapshot                                      |
| END_INTERVAL_TIME   | End time of the snapshot                                        |
| FLUSH_ELAPSED       | Time taken to generate the snapshot                             |
| SNAP_LEVEL          | Reserved field                                                 |
| STATUS              | Reserved field                                                 |
| ERROR_COUNT         | Reserved field                                                 |
| INST_CHANGE_TIME    | Database instance change timestamp |
| SRC_DBID            | The source database ID of the performance data |
| DATABASE_ROLE       | Database Role<br>\*   PRIMARY: Primary database <br>\*   STANDBY: Physical standby database <br>\*   LOGICAL STANDBY: Logical standby database  |
| GROUP_ID            | Group ID |
| GROUP_NODE_ID       | Node ID within the group |

## Generate Report

Generating a report involves comparing two snapshots to create a performance report according to different dimensions. The calculation of the report is performed by the stored procedure ([DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).AWR_REPORT), which the user actively calls when a report is needed.

1. Connect to the database using the DBA user *yasql*.

    ```shell
    $ yasql sales/********@192.168.1.2:1688
    ```

2. Query snapshot information, including corresponding database ID, instance identifier, snapshot ID, etc. This document takes the latest two snapshots as an example.

    ```sql
    SELECT dbid,src_dbid,snap_id,instance_number,group_id,group_node_id FROM sys.wrm$_snapshot ORDER BY snap_id DESC LIMIT 2;
        DBID    SRC_DBID     SNAP_ID INSTANCE_NUMBER    GROUP_ID GROUP_NODE_ID
    ----------- ----------- ----------- --------------- ----------- -------------
    121922913   121922913         144               1           0             0
    121922913   121922913         143               1           0             0
    ```

3. Retrieve the performance report (HTML file) using the following methods; please specify the corresponding file path as needed. This document uses the current path when executing the *yasql* command as an example.

    ::: tabs

    == Directly save output results as a file

    The file extension should be specified as .html; for file naming rules, please refer to the [yasql usage guide](../../Tools Guide/yasql/User Guide for yasql.md#spool).
    

```sql
-- 1. Turn on the buffer information print switch
set serveroutput on

-- 2. Enable output result saving as a file and specify the information for creating a new target file, for example, the filename is awr.html
spool awr.html create

-- 3. Generate the performance report based on the database ID, instance identifier, start snapshot ID, and end snapshot ID obtained from the above steps
exec dbms_awr.awr_report(121922913,1,143,144);

-- 4. End the output to the file and exit the session.
spool off
exit
```

    == Execute .sql file and then convert it to .html file

```shell
# 1. Exit the session
SQL> exit
# 2. Create awr.sql file, write the following statements related to generating performance reports into the file and save it
$ vi awr.sql

set serveroutput on
exec dbms_awr.awr_report(121922913,1,143,144);

# 3. Execute the above .sql file and convert the result to .html file
$ yasql sales/********@192.168.1.2:1688 -f -e awr.sql > awr.html 2>&1
```
    :::

4. Check the newly generated awr.html file in the corresponding path.

    The awr.html file is the performance report covering the start and end snapshot intervals.

    ```shell
    $ ll
    -rw-rw-r--  1 yashan yashan    110326 Jul   2 17:11 awr.html
    ```

## Report Content

The following is an example of the performance report generated by YashanDB displayed in web format:

![](./image/wrr.png)

### Information

#### Database Information

Related information about the current database.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| DB Name            | Database name                                              |
| DB Id              | Database ID                                                |
| Unique Name        | Compatible field, no meaning                                |
| Role               | Database role<br/>* PRIMARY: Primary<br/>* STANDBY: Standby<br/>* CASCADE STANDBY: Cascade Standby |
| Release            | Database version number                                    |
| Instance           | Compatible field, no meaning                                |
| Inst ID            | Instance identifier                                         |
| Startup Time       | Database startup time                                      |

#### Host Information

Related information about the server where the database is located.

|Information Item |Meaning |
| ---------- | ------------------ |
| Host Name          | Server name                                                 |
| Platform           | Server operating system platform                             |
| CPUs               | Number of logical CPUs in the server                        |
| Cores              | Number of CPU cores in the server                           |
| Sockets            | Number of physical CPUs in the server                       |
| Memory(GB)        | Size of memory in the server                                 |

#### Snapshot Information

Relevant information about the snapshots covered in this performance report.

|Information Item |Meaning |
| --------------- | ------------------------------------ |
| Snap Id             | Start and end snapshot IDs                                |
| Snap Time           | Start and end snapshot times                              |
| Sessions            | Number of sessions at the time of snapshot                 |
| Cursor/Sessions     | Average number of cursors per session at the time of snapshot |
| Elapsed             | Time difference between start and end snapshots, i.e., duration covered by the performance report |
| DB Time             | Total SQL execution time during the start and end snapshots |

#### Load Profile

Average load information of the system counted per second and per transaction (DB Time and DB CPU also counted per execution and per call).

|Information Item |Meaning |
| ---------------------- | -------------------------------- |
| DB Time(s)                   | Total SQL execution time            |
| DB CPU(s)                    | CPU time spent on SQL execution     |
| Redo size (bytes)            | Size of the generated redo          |
| Logical read (blocks)        | Number of logical reads             |
| Block changes                 | Changes described by data blocks (units: blocks)           |
| Physical read (blocks)       | Number of physical reads            |
| Physical write (blocks)      | Number of physical writes           |
| Read IO requests              | Number of physical read IO requests  |
| Write IO requests             | Number of physical write IO requests |
| Read IO (MB)                 | Amount of data for physical reads    |
| Write IO (MB)                | Amount of data for physical writes    |
| scan rows                    | Number of rows scanned in tables      |
| User calls                   | Number of user-level calls            |
| Parses (SQL)                 | Total number of SQL parses (soft + hard) |
| Hard parses (SQL)            | Number of hard SQL parses            |
| Logons                       | Number of current logged-in sessions   |
| Executes (SQL)               | Number of SQL command executions      |
| Rollbacks                    | Number of transaction rollbacks        |
| Transactions                 | Number of transactions (rollback + commit) |

### Report Summary

#### Top 10 Foreground Events by Total Wait Time

Top 10 wait events information (by %DB Time); for an explanation of wait events, please refer to [Wait Events](../../Reference Manual/Wait Events).

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Wait Class                     | Category of wait event          |
| Waits                          | Number of waits                |
| Total Wait Time (sec)         | Total wait time                |
| Avg Wait Time                  | Average wait time              |
| % DB Time                      | Percentage of Total Wait Time in DB Time |

#### Wait Classes by Total Wait Time

Top 10 wait event categories information, similar meanings as in Top 10 Foreground Events by Total Wait Time. The Avg Active Sessions item is a compatible field and has no actual meaning.

For an explanation of wait event categories, please refer to [Database Performance Metrics](../Instance Performance Diagnosis and Tuning/Database Performance Metrics).

#### SGA Memory Summary

Records global memory information.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Memory Item Name               | Name of the memory item     |
| Memory Size (byte)            | Memory size (unit: bytes)   |

#### Instance Efficiency Percentages (Target 100%)

Records instance hit rate information.

|Information Item |Meaning |
|---------------|---------------------|
| Redo NoWait %                 | Proportion of no-wait in redo log writing |
| Buffer Hit %                  | Cache hit rate, proportion of successfully reading data pages from Cache |
| Soft Parse %                  | Percentage of soft parses      |

#### Execute Statistics

Records execution and parsing counts of SQL.

|Information Item |Meaning |
|---------------|---------------------|
| Execute Count                  | Number of executions           |
| Hard Parse Count               | Number of hard parses          |
| Soft Parse Count               | Number of soft parses          |

### SQL Statistics

#### SQL ordered by Elapsed Time

Top SQL information arranged by total time of execution (Elapsed Time); see report content for explanation of information items.

#### SQL ordered by CPU Time

Top SQL information arranged by CPU time spent on execution (CPU Time); see report content for explanation of information items.

#### SQL ordered by User I/O Wait Time

Top SQL information arranged by User I/O wait time (User I/O Time); see report content for explanation of information items.

#### SQL ordered by Gets

Top SQL information arranged by logical reads (Buffer Gets); see report content for explanation of information items.

#### SQL ordered by Reads

Top SQL information arranged by physical reads (Physical Reads, in bytes); see report content for explanation of information items.

#### SQL ordered by Executions

Top SQL information arranged by execution counts (Executions); see report content for explanation of information items.

#### SQL ordered by Parse Calls

Top SQL information arranged by soft parse counts (Parse Calls); see report content for explanation of information items.

#### SQL ordered by Sharable Memory

Top SQL information arranged by sharable memory (Sharable Mem); see report content for explanation of information items.

### Memory Statistics

#### Virtual Memory Total Statistics

Records VM usage, counting only HEAP tables.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Total Blocks                   | Total number of memory pages  |
| Free Blocks                    | Number of free memory pages    |
| Opened Blocks                  | Number of opened memory pages   |
| Closed Blocks                  | Number of closed memory pages   |
| Swapped Out Blocks             | Number of memory pages swapped out to disk |
| Ctrl Blocks                    | Number of control pages         |

#### System-level Information

Records system-level statistics.

|Information Item |Meaning |
|--------------------| ------------------------------ |
| Name                           | Name of the statistics item   |
| Value                          | Size of memory (unit: bytes)  |

#### VM Buffer Used

Records accumulated usage of VM Buffer at the session level (sorted by Alloc Count).

|Information Item |Meaning |
|--------------------|---------------|
| SID                            | Session ID                   |
| Alloc Count                    | Total number of allocations   |
| Open Count                     | Total number of openings      |
| Close Count                    | Total number of closures      |
| Free Count                     | Total number of releases      |
| Swap Out Count                 | Total number of swaps out to disk |
| Swap In Count                  | Total number of swaps in from disk |
| IO Wait Count                  | Total number of IO waits      |
| Extend Count                   | Total number of VM page extensions  |

#### VM Current Open

Records current VM usage at the session level (sorted by Open Count).

|Information Item |Meaning |
|--------------------|--------------|
| SID                            | Session ID                   |
| Open Count                     | Current number of opened pages   |
| Close Count                    | Current number of closed records |
| Swap Out Count                 | Current number of pages being swapped out |

#### VM Current Swap

Records current VM swap information at the session level (sorted by Swap Out Count).

|Information Item |Meaning |
|--------------------|--------------|
| SID                            | Session ID                   |
| Open Count                     | Current number of opened pages   |
| Close Count                    | Current number of closed records |
| Swap Out Count                 | Current number of pages being swapped out |

### YAC Statistics

> **Note**: 
>
> YAC Statistics report is only generated under YAC Deployment.

#### YAC Summary

Overview of YAC information.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Number of Instances            | Number of YAC instances |
| Number of GCS Tasks           | Number of GCS background threads |

#### Global Cache Load Profile

Statistics of global buffer, counted per second and per transaction.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Global Cache blocks received    | Number of pages received from other instances |
| Global Cache blocks served      | Number of pages sent to other instances |
| GRC messages received           | Number of GRC messages received         |
| GCS messages received           | Number of GCS messages received         |
| GLS messages received           | Number of GLS messages received         |
| GRC messages sent               | Number of GRC messages sent             |
| GCS messages sent               | Number of GCS messages sent             |
| GLS messages sent               | Number of GLS messages sent             |
| DBWR Fusion writes             | Number of fusion writes (reserved)       |

#### Global Cache Efficiency Percentages

Percentage of global buffer access.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Buffer access - local cache %  | Percentage of local buffer access |
| Buffer access - remote cache % | Percentage of remote buffer access |
| Buffer access - disk %         | Percentage of disk buffer access  |

#### Global Cache and Enqueue Services- Workload Characteristics

Statistics of global buffer and enqueue services.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Avg global cache cr block receive time (us) | Average time to receive CR blocks from buffer |
| Avg global cache current block receive time (us) | Average time to receive current blocks from buffer |
| Avg global cache current block flush time (us) | Average time to flush data blocks |

#### YAC Sys Stats

YAC-related statistics in system-level statistics; explanations for the information items can be found in [Statistics](../../Reference Manual/Statistics Information).

### Lock Statistics

#### Spinlock Summary

Records spin counts information for spinlocks.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Name                           | Name of the lock           |
| Spincount                      | Number of times CPU must wait during each spin |
| Times                          | Total number of spin waits  |