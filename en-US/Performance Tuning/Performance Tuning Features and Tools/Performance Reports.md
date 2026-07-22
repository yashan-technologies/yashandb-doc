YashanDB provides rich performance report tools to help users understand the running status of the database, locate performance bottlenecks, and optimize database performance, including AWR report and ASH report.

- **AWR Report**: YashanDB periodically saves a snapshot of the database status data into the WRM$_SNAPSHOT table during its operation. The status data includes wait events, metric data, space usage statistics, SQL state information, and other critical database statistics. Performance reports reflect the entire database status over a period by comparing two History snapshots, providing a basis for post-event analysis and diagnosis of database anomalies.

- **ASH Report**: ASH report is a lightweight performance diagnostic tool provided by YashanDB, which collects performance data by periodically sampling the active session status to collect performance data, reflecting the load of the database.

## AWR Report

### Snapshot

By default, snapshots are created by the system at regular intervals (default is every hour; this value can be modified using [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).MODIFY_SNAPSHOT_SETTINGS).

To diagnose performance bottlenecks, users may need to create a snapshot instantaneously at a specified point in time (e.g., before and after executing a SQL statement) to generate a performance report. At this point, a snapshot can be manually created by calling the [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).CREATE_SNAPSHOT procedure.

Snapshot data will be automatically cleaned by the system after a certain period (default is 8 days; this value can also be modified using [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).MODIFY_SNAPSHOT_SETTINGS), or manually cleaned via [DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).DROP_SNAPSHOT_RANGE.

The main information about the snapshot is shown in the following table:

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

### Generate AWR Report

Generating an AWR report involves comparing two snapshots to create a performance report according to different dimensions. The calculation of the report is performed by the stored procedure ([DBMS_AWR](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_AWR).AWR_REPORT), which the user actively calls when a report is needed.

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

### Report Content

The following is an example of the performance report generated by YashanDB displayed in web format:

![](./image/wrr.png)

#### Information

##### Database Information

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

##### Host Information

Related information about the server where the database is located.

|Information Item |Meaning |
| ---------- | ------------------ |
| Host Name          | Server name                                                 |
| Platform           | Server operating system platform                             |
| CPUs               | Number of logical CPUs in the server                        |
| Cores              | Number of CPU cores in the server                           |
| Sockets            | Number of physical CPUs in the server                       |
| Memory(GB)        | Size of memory in the server                                 |

##### Snapshot Information

Relevant information about the snapshots covered in this performance report.

|Information Item |Meaning |
| --------------- | ------------------------------------ |
| Snap Id             | Start and end snapshot IDs                                |
| Snap Time           | Start and end snapshot times                              |
| Sessions            | Number of sessions at the time of snapshot                 |
| Cursor/Sessions     | Average number of cursors per session at the time of snapshot |
| Elapsed             | Time difference between start and end snapshots, i.e., duration covered by the performance report |
| DB Time             | Total SQL execution time during the start and end snapshots |

##### Load Profile

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
| Logons                       | Number of database connections Established   |
| Executes (SQL)               | Number of SQL command executions      |
| Rollbacks                    | Number of transaction rollbacks        |
| Transactions                 | Number of transactions (rollback + commit) |

#### Report Summary

##### Top 10 Foreground Events by Total Wait Time

Top 10 wait events information (by %DB Time); for an explanation of wait events, please refer to [Wait Events](../../Reference Manual/Wait Events).

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Wait Class                     | Category of wait event          |
| Waits                          | Number of waits                |
| Total Wait Time (sec)         | Total wait time                |
| Avg Wait Time                  | Average wait time              |
| % DB Time                      | Percentage of Total Wait Time in DB Time |

##### Wait Classes by Total Wait Time

Top 10 wait event categories information, similar meanings as in Top 10 Foreground Events by Total Wait Time. The Avg Active Sessions item is a compatible field and has no actual meaning.

For an explanation of wait event categories, please refer to [Database Performance Metrics](../Instance Performance Diagnosis and Tuning/Database Performance Metrics).

##### SGA Memory Summary

Records global memory information.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Memory Item Name               | Name of the memory item     |
| Memory Size (byte)            | Memory size (unit: bytes)   |

##### Instance Efficiency Percentages (Target 100%)

Records instance hit rate information.

|Information Item |Meaning |
|---------------|---------------------|
| Redo NoWait %                 | Proportion of no-wait in redo log writing |
| Buffer Hit %                  | Cache hit rate, proportion of successfully reading data pages from Cache |
| Soft Parse %                  | Percentage of soft parses      |

##### Execute Statistics

Records execution and parsing counts of SQL.

|Information Item |Meaning |
|---------------|---------------------|
| Execute Count                  | Number of executions           |
| Hard Parse Count               | Number of hard parses          |
| Soft Parse Count               | Number of soft parses          |

#### SQL Statistics

##### SQL ordered by Elapsed Time

Top SQL information arranged by total time of execution (Elapsed Time); see report content for explanation of information items.

##### SQL ordered by CPU Time

Top SQL information arranged by CPU time spent on execution (CPU Time); see report content for explanation of information items.

##### SQL ordered by User I/O Wait Time

Top SQL information arranged by User I/O wait time (User I/O Time); see report content for explanation of information items.

##### SQL ordered by Gets

Top SQL information arranged by logical reads (Buffer Gets); see report content for explanation of information items.

##### SQL ordered by Reads

Top SQL information arranged by physical reads (Physical Reads, in bytes); see report content for explanation of information items.

##### SQL ordered by Executions

Top SQL information arranged by execution counts (Executions); see report content for explanation of information items.

##### SQL ordered by Parse Calls

Top SQL information arranged by soft parse counts (Parse Calls); see report content for explanation of information items.

##### SQL ordered by Sharable Memory

Top SQL information arranged by sharable memory (Sharable Mem); see report content for explanation of information items.

#### Memory Statistics

##### Virtual Memory Total Statistics

Records VM usage, counting only HEAP tables.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Total Blocks                   | Total number of memory pages  |
| Free Blocks                    | Number of free memory pages    |
| Opened Blocks                  | Number of opened memory pages   |
| Closed Blocks                  | Number of closed memory pages   |
| Swapped Out Blocks             | Number of memory pages swapped out to disk |
| Ctrl Blocks                    | Number of control pages         |

##### System-level Information

Records system-level statistics.

|Information Item |Meaning |
|--------------------| ------------------------------ |
| Name                           | Name of the statistics item   |
| Value                          | Size of memory (unit: bytes)  |

##### VM Buffer Used

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

##### VM Current Open

Records current VM usage at the session level (sorted by Open Count).

|Information Item |Meaning |
|--------------------|--------------|
| SID                            | Session ID                   |
| Open Count                     | Current number of opened blocks   |
| Close Count                    | Current number of closed blocks |
| Swap Out Count                 | Current number of pages being swapped out |

##### VM Current Swap

Records current VM swap information at the session level (sorted by Swap Out Count).

|Information Item |Meaning |
|--------------------|--------------|
| SID                            | Session ID                   |
| Open Count                     | Current number of opened blocks   |
| Close Count                    | Current number of closed blocks |
| Swap Out Count                 | Current number of pages being swapped out |

#### Lock Statistics

##### Spinlock Summary

Records spin counts information for spinlocks.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Name                           | Name of the lock           |
| Spincount                      | Number of times CPU must wait during each spin |
| Times                          | Total number of spin waits  |

#### Segment Statistics

The Segment Statistics metrics are only available in standalone deployment and YAC deployment.

The Segment Statistics metrics only return the top 30 records.

##### Segments by Logical Reads

Counts the logical reads for segments. Each read of a block counts as one, and calculates the proportion of these reads relative to the total number of similar operations between two snapshots.

When the logical read metric is high, it is necessary to confirm if the corresponding database objects are under normal business load. Execution plans can be used to check if business SQL lacks appropriate indexes leading to full table scans, or if execution plan errors cause repeated reads.

##### Segments by Physical Reads

Counts the physical reads for segments. Each read of a block counts as one, and calculates the proportion of these reads relative to the total number of similar operations between two snapshots.

If the physical read metric is high, business performance may be affected.

When the physical read metric is high, it indicates the database may not be caching data effectively, requiring frequent I/O from disk. The following tuning methods can be considered:

- Tune the business SQL.
- Adjust the size of the DATA_BUFFER_SIZE.
- If tables and indexes are small, consider configuring them as in-memory tables.
- Rebuild tables or indexes.

##### Segments by Buffer Busy Waits

Counts the number of Buffer Busy Waits occurrences on segments and calculates the proportion of this count relative to the total number of similar wait events between two snapshots.

When this metric is high, it indicates many sessions are simultaneously requesting cached data from this segment, leading to wait events. Hot blocks exist on the data blocks of this segment.

Hot blocks may form due to repeated scans of hot tables, reverse key indexes, or indexes on monotonically increasing sequences. The following tuning methods can be considered:

- For hot tables, consider using hash partitioning to scatter data blocks.
- For index hotspots caused by increasing sequences, consider using reverse key indexes.

##### Segments by Xslot Waits

Counts the number of transaction waits on all data blocks in a segment caused by insufficient transaction slots (Xslot), and calculates the proportion of this count relative to the total number of similar wait events between two snapshots.

When this metric is high, analyze if there are high-concurrency transactions modifying the same business table. If so, consider adjusting the INITRANS parameter of the table or index, or rebuilding the object.

##### Segments by Row Lock Waits

Counts the number of row-level lock waits on segments and calculates the proportion of this count relative to the total number of similar wait events between two snapshots.

When this metric is high, concurrency contention and transaction blocking may be occurring. It is necessary to check if the business logic of the corresponding transactions is reasonable. Methods like selective commit, queuing, or application-level sharding can be used to reduce contention for the same row of data in the database.

##### Segments by Physical Read Requests

Counts the number of physical read requests for segments. Each request to read a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

This metric helps identify hot blocks and locate objects requiring optimization.

##### Segments by DB Blocks Changes

Counts the number of data block changes for segments. Each change of a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

If this metric is high, it indicates objects in this segment are updated frequently. It can be used to help identify hot tables or indexes, and consider optimization strategies like optimizing index structure or table partitioning.

##### Segments by Global Cache CR Blocks Received

In YAC deployment, this metric counts the number of CR blocks received. When an instance needs to read a data block from another instance, it sends a CR request, and the remote instance returns the Consistent Read version of that data block.

This metric counts, for each segment under this instance, the number of requests for database blocks in the CR version. Each request for a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

When this metric is high, concurrent read contention may be occurring. Consider analyzing the causes and solutions from the following dimensions:

- Analyze the blocking causes of slow business SQL and perform SQL rewriting.
- Many full table scans: If the requester instance frequently reads and writes the same set of data blocks, consider if the requester is performing full table scans, thus requesting a large number of data blocks from remote instances. Analyze if the requester's business can be optimized by adding indexes, partition pruning, or materialized view caching.
- Hot block contention: If multiple instances frequently access and modify the same set of data blocks, and the accessed objects are small, consider reverse key indexes for index objects, and optimization methods like caching, data partitioning, and instance affinity for small table objects.

##### Segments by Global Cache Current Blocks Received

In YAC deployment, to perform data modifications, this instance needs to request the Current ownership of data blocks from the caches of other instances.

This metric counts, for each segment under this instance, the number of requests for data blocks for Current ownership. Each request for a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

When this metric is high, concurrent write transaction conflicts may occur. Consider analyzing the causes and solutions from the following dimensions:

- For index hotspots, consider distributing index keys to different data blocks via hash partitioning, or using reverse key index technology.
- For small table hotspot scenarios, consider instance affinity, application-level queuing, or summarizing updates through materialized views.
- For large object hotspot scenarios, consider performance tuning of SQL statements, or optimizing the application architecture.

##### Segments by Global Cache Remote Grants

In YAC deployment, to operate on data, this instance requests CR and Current grants for data blocks from the master node.

This metric counts, for each segment under this instance, the number of requests for grants for data blocks from the master node. Each request for a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

This metric typically reflects whether there is high network communication overhead in the cluster. When this metric is high, consider avoiding hot blocks on a single instance by distributing data as evenly as possible, or evaluate solutions utilizing instance affinity features.

##### Segments by Global Cache Buffer Busy

In YAC deployment, counts the number of waits on this instance caused by cache contention on remote instances, and calculates the proportion of this count relative to the total number of similar wait events between two snapshots.

For tuning hot blocks, consider methods like evenly distributing data, adjusting block size, and increasing INITRANS.

##### Segments by Table Scans

This metric counts the number of full scans that occurred on data segments. Each scan of a block counts as one, and calculates the proportion of this count relative to the total number of similar operations between two snapshots.

Business performance may be poor when full scans occur. When business performance is poor, consider the following tuning methods:

- SQL optimization and rewriting.
- Update table statistics.
- Parallel query.

##### Segments by Space Alloc Sizes

This metric counts the size change of data segments between two snapshots and calculates the proportion of this change relative to the total occupied space of all data segments.

Regularly monitor the database's space growth trend and establish an automated space reclamation mechanism.

#### YAC Statistics

> **Note**: 
>
> YAC Statistics report is only generated under YAC Deployment.

##### YAC Summary

Overview of YAC information.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Number of Instances            | Number of YAC instances |
| Number of GCS Tasks           | Number of GCS background threads |

##### Global Cache Load Profile

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

##### Global Cache Efficiency Percentages

Percentage of global buffer access.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Buffer access - local cache %  | Percentage of local buffer access |
| Buffer access - remote cache % | Percentage of remote buffer access |
| Buffer access - disk %         | Percentage of disk buffer access  |

##### Global Cache and Enqueue Services- Workload Characteristics

Statistics of global buffer and enqueue services.

|Information Item |Meaning |
| -------------------- | ------------------------------ |
| Avg global cache cr block receive time (us) | Average latency for retrieving CR blocks from remote instance buffer |
| Avg global cache current block receive time (us) | Average time to receive current blocks from buffer |
| Avg global cache current block flush time (us) | Average time to flush data blocks |

##### YAC Sys Stats

YAC-related statistics in system-level statistics; explanations for the information items can be found in [Statistics](../../Reference Manual/Statistics Information).

## ASH Report

> **Note**:
>
> - ASH data is persisted to the corresponding system tables and retained for 8 days by default.The value can be modified by [DBMS_WORKLOAD_REPOSITORY.md](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_WORKLOAD_REPOSITORY).MODIFY_SNAPSHOT_SETTINGS and automatically cleaned by the system. It can also be manually cleaned by [DBMS_WORKLOAD_REPOSITORY.md](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_WORKLOAD_REPOSITORY).DROP_SNAPSHOT_RANGE.

### Generate ASH Report

The calculation of the ASH Report is performed by the stored procedure ([DBMS_WORKLOAD_REPOSITORY](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_WORKLOAD_REPOSITORY)., which the user actively calls when a report is needed.

1. Connect to the database using the DBA user *yasql*.

    ```shell
    $ yasql sales/********@192.168.1.2:1688
    ```

2. Query the corresponding database ID, instance ID, and other information, taking the last two as an example.

    ```sql
    select  DATABASE_ID, INST_ID from GV$DATABASE;
        DATABASE_ID         INST_ID   
        -----------      -------------
         121922913             1          
    ```

3. Retrieve the performance report (HTML file) using the following methods; please specify the corresponding file path as needed. This document uses the current path when executing the *yasql* command as an example.

    ::: tabs

    == Directly save output results as a file

    The file extension should be specified as .html; for file naming rules, please refer to the [yasql usage guide](../../Tools Guide/yasql/User Guide for yasql.md#spool).
    

```sql
-- 1. Turn on the buffer information print switch
set serveroutput on

-- 2. Enable output result saving as a file and specify the information for creating a new target file, for example, the filename is ash.html
spool ash.html create

-- 3. Generate the performance report based on the database ID, instance identifier obtained from the above steps
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(121922913,1,'2026-03-05 10:10:10','2026-03-05 23:30:10');

-- 4. End the output to the file and exit the session.
spool off
exit
```

    == Execute .sql file and then convert it to .html file

```shell
# 1. Exit the session
SQL> exit
# 2. Create ash.sql file, write the following statements related to generating performance reports into the file and save it
$ vi ash.sql

set serveroutput on
exec DBMS_WORKLOAD_REPOSITORY.ASH_REPORT_HTML(121922913,1,'2026-03-05 10:10:10','2026-03-05 23:30:10');

# 3. Execute the above .sql file and convert the result to .html file
$ yasql sales/********@192.168.1.2:1688 -f -e ash.sql > ash.html 2>&1
```
    :::

4. Check the newly generated ash.html file in the corresponding path.

    The ash.html file is the performance report covering the start and end time intervals.

    ```shell
    $ ll
    -rw-rw-r--  1 yashan yashan    110326 Jul   2 17:11 ash.html
    ```

### Report Content

The following is an example of the performance report generated by YashanDB displayed in web format:

![](./image/ashr.png)

#### Information

##### Database Information

Related information about the current database.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| DB Name            | Database name                                              |
| DB Id              | Database ID                                                |
| Instance Name       | Instance name                                              |
| Instance Id        | Instance ID                                                |
| Release            | Version number                                             |
| YAC                | Whether it is a cluster                                    |
| Host               | Host name                                                  |

##### Memory Statistics

Contains CPU and SGA memory information.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| CPUs               | Number of CPU cores                                         |
| SGA Size           | Total SGA memory size                                       |
| Buffer Cache Size  | Size of Data buffer and percentage of SGA                   |
| Shared Pool Size  | Size of Shared Pool and percentage of SGA                    |
| ASH Buffer Size  | Size of ASH buffer and percentage of SGA                     |

##### Report Overview

Overview information of the report.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Analysis Begin Time       | Report analysis start time                                  |
| Analysis End Time         | Report analysis end time                                    |
| Elapsed Time             | Total time elapsed from start to end                        |
| Sample Count             | Number of sampled records from start to end                 |
| Average Active Sessions  | Average number of active sessions per second from start to end |
| Avg Active Sessions Per CPU | Average number of active sessions per CPU core per second from start to end |
| Data Source              | Data source for the report                                   |

#### Top Events

Describes the top waiting events in sampled session activity, categorized by user, background, and priority. Use the information in this section to identify waiting events that may cause transient performance issues. Contains three parts: Top User Events, Top Background Events, and Top Event. Top Background Events lists the top waiting events from background processes as a percentage of sampled session activity. The report content is similar to Top User Events.

##### Top User Events

Lists the top waiting events from user processes as a percentage of sampled session activity.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Event              | Event name                                                  |
| Event Class        | Event type name                                             |
| % Event            | Percentage of the event among all waiting events            |
| Avg Active Sessions | Average number of active sessions per second waiting for this event during the report analysis period |

##### Top Event

Lists the waiting event parameter values of the top waiting events as a percentage of sampled session activity, sorted by total waiting time percentage (% Event). For each waiting event, the values in the "P1 Value", "P2 Value", and "P3 Value" columns correspond to the waiting event parameters displayed in the "Parameter 1", "Parameter 2", and "Parameter 3" columns.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Event              | Event name                                                  |
| %Event             | Percentage of the event among all waiting events            |
| %Activity          | Percentage of the event in total sampled data               |

#### Load Profile

Describes the load analyzed in sampled session activity. Use the information in this section to identify services, clients, or SQL command types that may be causing transient performance issues. Includes four parts: Top Service/Module, Top Client IDs, Top SQL Command Types, and Top Phases of Execution.

##### Top Service/Module

Lists the top services and modules as a percentage of sampled session activity.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Module             | Value set by DBMS_APPLICATION_INFO.SET_MODULE               |
| %Activity          | Percentage of current Service or Module in total sampled data |
| Action             | Value set by DBMS_APPLICATION_INFO.SET_MODULE or DBMS_APPLICATION_INFO.SET_ACTION |
| %Action            | Percentage of Action in its Service/Module                  |

##### Top Client IDs

Lists the top clients as a percentage of sampled session activity based on client ID (application-specific identifier for database sessions).

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Client Id          | Value set by DBMS_SESSION.SET_IDENTIFIER                    |
| %Activity          | Percentage of total sampled data                             |
| Avg Active Sessions | Average number of sessions per second                        |
| User               | User name                                                   |
| Program            | Program name                                                |

##### Top SQL Command Types

Lists the top SQL command types (such as SELECT or UPDATE commands) as a percentage of sampled session activity.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Command Type       | SQL type name                                               |
| Distinct SQLIDs   | Number of distinct SQLs                                     |
| %Activity          | Percentage of total sampled data                             |
| Avg Active Sessions | Average number of SQLs of this type executed per second during the report analysis period |

##### Top Phases of Execution

Lists the top execution phases (such as compilation and execution of SQL, PL, and Java) as a percentage of sampled session activity.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Phase of Execution       | Current execution phase, provided by TIME_MODEL              |
| %Activity          | Percentage of total sampled data                             |
| Avg Active Sessions | Average number of sessions in this phase per second          |

#### Top SQL

Describes the top SQL statements in sampled session activity. Use this information to identify high-load SQL statements that may be causing transient performance issues. Contains Top SQL with Top Events, Top SQL with Top Row Sources, and Top Parsing Module/Action.

##### Top SQL with Top Events

Lists the top SQL statements as a percentage of sampled session activity and the waiting events encountered by these SQL statements. The results are sorted by SQL ID and Event.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| SQL ID             | SQL identifier of the SQL statement                         |
| PlanHash          | Hash value of the SQL                                       |
| Sampled # of Executions | Number of times the SQL was sampled                         |
| %Activity          | Percentage of the SQL in total sampled data                 |
| Event              | Event name                                                  |
| %Event             | Percentage of the event in this SQL                         |
| Top Row Source     | The most time-consuming execution phase (operator) of the SQL |
| %RwSrc            | Percentage of this execution phase                           |
| SQL Text           | SQL statement                                               |

##### Top SQL with Top Row Sources

Lists the top SQL statements as a percentage of sampled session activity and their detailed execution phase information. Use this information to identify which execution phase (operator) of the SQL significantly affected the SQL elapsed time.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| SQL ID             | SQL identifier of the SQL statement                         |
| PlanHash          | Hash value of the SQL                                       |
| Sampled # of Executions | Number of times the SQL was sampled                         |
| %Activity          | Percentage of the SQL in total sampled data                 |
| Row Source         | Current execution phase (operator)                          |
| %RwSrc            | Percentage of this phase in the SQL                         |
| Top Event          | The most time-consuming waiting event of the SQL            |
| %Event             | Percentage of the waiting event                              |
| SQL Text           | SQL statement                                               |

##### Top Parsing Module/Action

Lists the top modules and actions as a percentage of sampled session activity when parsing SQL statements.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Module             | Value set by DBMS_APPLICATION_INFO.SET_MODULE               |
| Action             | Value set by DBMS_APPLICATION_INFO.SET_MODULE or DBMS_APPLICATION_INFO.SET_ACTION |
| %Activity          | Percentage of total sampled data                             |
| Event              | Event name                                                  |
| %Event             | Percentage of the event in the module/action                |

#### Top PL/SQL

Lists the top PL as a percentage of sampled session activity.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| PLSQL Entry Subprogram | Entry program of PL, obtained from v$session/v$active_session_history.PLSQL_ENTRY_SUBPROGRAM_ID |
| %Activity          | Percentage of total sampled data                             |
| PLSQL Current Subprogram | Currently executing subprogram, obtained from v$session/v$active_session_history.PLSQL_SUBPROGRAM_ID |
| %Current           | Percentage of the subprogram in the entry program            |

#### Top Sessions

Describes sessions that are waiting for specific waiting events. Use this information to identify sessions with the highest percentage of sampled session activity that may be causing transient performance issues. Contains: Top Sessions.

##### Top Sessions

Lists sessions that are waiting for specific waiting events and rank as the top percentage of sampled session activity. The final data is sorted by Sid, Serial#, and Event.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Sid,Serial#       | Session ID and session serial number                        |
| %Activity          | Percentage of the session in total sampled data              |
| Event              | Waiting event                                               |
| %Event             | Percentage of the waiting time in the session                |
| User               | User name                                                   |
| Program            | Client program name                                         |
| #Samples Active    | Number of samples                                           |
| XIDs               | Number of different XIDs                                    |

#### Activity Over Time

One of the most informative parts of the ASH report. This section is particularly useful for analyzing longer time periods because it provides detailed insights into activity and load profiles during the analysis period. Divided into 10 time slots. The size of each time slot varies based on the duration of the analysis period. The first and last time slots are usually irregular in size. All internal time slots are equal in size and can be compared with each other.

|Information Item |Meaning |
| ------------ | ------------------------------------------------------------ |
| Slot Time Duration       | Duration of the time slot                                   |
| Slot Count         | Number of sampled sessions in the time slot                 |
| Event              | Top three waiting events in the time slot                   |
| Event Count        | Number of ASH samples waiting for the waiting event          |
| %Event             | Percentage of ASH samples for the waiting event during the entire analysis period |

