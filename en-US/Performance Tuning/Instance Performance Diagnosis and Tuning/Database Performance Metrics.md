Database performance needs to be reflected through certain metrics. In addition to the application's own metrics, YashanDB also provides a set of metrics for measuring performance, mainly including database load and various resource usage. Understanding these metrics can help diagnose performance bottlenecks and serve as a method for measuring optimization effects.

YashanDB provides statistics categorized into four different levels and dimensions:

- System-level statistics: From the perspective of the database instance, these provide metrics for analyzing overall performance, including statistics from various components and modules.
- Session-level statistics: Since performance bottlenecks differ among sessions, metrics are provided to analyze the performance of each session.
- Statement-level statistics: These metrics analyze the performance of SQL statements from the perspective of individual SQL statements.
- Wait event statistics: These metrics analyze performance from the perspective of blocking caused by waiting for certain resources.

## System-Level Statistics

System-level performance metrics provide statistics on key internal activities occurring during the operation of YashanDB, recorded in terms of time or frequency. Each statistic reflects some aspect of database performance to varying degrees. In practical use, it is often necessary to combine multiple statistics to identify the root cause of performance issues. For specific meanings of each statistic, please refer to the reference manual section [statistics](../../Reference Manual/Statistics Information).

Users can query the values of each statistic using the V$SYSSTAT view and see how the statistics are classified based on functionality:

|Category ID |Category Name |Description |
| :------- | :------- | :---------------------------------------------------------- |
| 1           | USER          | Statistics related to user execution, such as transaction commit counts and SQL request counts. |
| 2           | REDO          | Statistics related to redo logs, reflecting the system's redo resource usage. |
| 8           | CACHE         | Statistics related to various caches within the database. |
| 64          | SQL           | Statistics related to SQL execution, such as execution counts of different DMLs and sort counts. |
| 128         | DEBUG         | Statistics used for internal debugging, generally not of concern to users. |

## Session-Level Statistics

Session-level statistics can be obtained from the V$SESSTAT view (this view only provides the ID of the statistics; the specific names need to be referenced from the V$STATNAME view). If only current session statistics are of interest, they can be retrieved from the V$MYSTAT view.

The session-level statistics and classifications are similar to those at the system level.

## Statement-Level Statistics

Statement-level statistics are related to SQL and can be accessed through the V$SQL view. The fields of interest are as follows:

|Field |Type |Description |
| :---------------------- | :------ | :----------------------------------------- |
| SHARABLE_MEM             | INTEGER | The size of shared memory used in the SQL Pool (unit: Byte) |
| FETCHES                  | BIGINT  | The fetch count of the SQL statement                    |
| EXECUTIONS               | BIGINT  | The number of executions after being loaded into the buffer cache |
| ROWS_PROCESSED           | BIGINT  | The total number of rows returned by the SQL statement      |
| PARSE_CALLS              | BIGINT  | The number of parse calls                     |
| DISK_READS               | BIGINT  | The number of disk reads                      |
| BUFFER_GETS              | BIGINT  | The number of buffer reads                    |
| PHYSICAL_READ_REQUESTS   | BIGINT  | The number of physical read I/O requests issued by SQL  |
| PHYSICAL_READ_BYTES      | BIGINT  | The number of bytes read from disk by SQL    |
| PHYSICAL_WRITE_REQUESTS  | BIGINT  | The number of physical write I/O requests issued by SQL  |
| PHYSICAL_WRITE_BYTES     | BIGINT  | The number of bytes written to disk by SQL    |
| USER_IO_WAIT_TIME        | BIGINT  | User I/O wait time (unit: microseconds)      |
| PLSQL_EXEC_TIME          | BIGINT  | PL/SQL execution time (unit: microseconds)   |
| CPU_TIME                 | BIGINT  | CPU time spent on parsing/executing/retrieving (unit: microseconds) |
| ELAPSED_TIME             | BIGINT  | Total time spent on parsing/executing/retrieving (unit: microseconds) |

## Wait Event Statistics

During database operation, waits can occur due to certain key activities (such as I/O), which generate an event known as a wait event. Statistics on wait events can provide feedback on database performance bottlenecks; for example, if there are many wait events related to lock contention, it may indicate that locks are becoming a bottleneck in the database.

YashanDB categorizes wait events into the following types, with each category containing one or more specific wait events:

- Application: Waits generated by the application, such as row lock waits caused by different application layer transactions.
- Concurrency: Waits caused by resources within the database, such as latch contention waits.
- Commit: Waits caused by redo log synchronization during transaction commits.
- User I/O: Waits caused by user threads that result in I/O, such as I/O waits from reading data blocks.
- System I/O: Waits caused by I/O from background threads in the database, such as I/O waits caused by the Database Writer thread synchronizing dirty blocks to disk.
- Other: Other types of waits.
- Idle: Waits while the database is waiting for a client message, indicating that the session is in an idle state.
- Network: Waits caused by network transmission, such as waits that occur during data transmission over the network.
- Configuration: Waits caused by database instance configuration, such as waits that occur when configuring redo size, undo size, or DATA BUFFER size.
- Cluster: Cluster wait events, such as waits for responses during inter-node interactions.
- Distributed: Distributed wait events, such as CN waiting for responses from other nodes.

Each type of wait event represents a category of issues and corresponds to specific optimization approaches. Users can access various categories of wait event statistics through the V$SYSTEM_WAIT_CLASS view, which distinguishes between foreground and background threads. Generally, performance issues are primarily reflected in the statistics of foreground threads.

The V$SYSTEM_EVENT view provides detailed statistics on specific wait events, including the number of waits, wait times, and other dimensions. For descriptions of all wait events in YashanDB and methods of analysis, please refer to the reference manual section [Wait Events](../../Reference Manual/Wait Events).