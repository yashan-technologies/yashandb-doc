This view displays all currently waiting event statistics. The definitions and descriptions of specific waiting events can be found in the [Wait Events](../../Wait Events) document.

|Field |Type |Description |
| --- | --- | --- |
| SID | SMALLINT | Session ID |
| EVENT | VARCHAR(32) | Waiting event name |
| TOTAL_WAITS | BIGINT | Total wait count |
| TOTAL_TIMEOUTS | BIGINT | Total timeout count |
| TIME_WAITED | BIGINT | Wait time (in milliseconds) |
| AVERAGE_WAIT | NUMBER | Average wait time (in milliseconds) |
| TIME_WAITED_MICRO | BIGINT | Wait time (in microseconds) |
| TOTAL_WAITS_FG | BIGINT | Foreground wait count |
| TOTAL_TIMEOUTS_FG | BIGINT | Foreground timeout count |
| TIME_WAITED_FG | BIGINT | Foreground wait time (in milliseconds) |
| AVERAGE_WAIT_FG | NUMBER | Foreground average wait time (in milliseconds) |
| TIME_WAITED_MICRO_FG | BIGINT | Foreground wait time (in microseconds) |
| EVENT_ID | INTEGER | Waiting event ID |
| WAIT_CLASS | VARCHAR(16) | Waiting event category<br>\*   APPLICATION: Waits caused by the application, such as row lock waits from different application layer transactions.<br>\*   CONCURRENCY: Waits caused by internal database resources, such as latch contention waits.<br>\*   COMMIT: Waits caused by redo log synchronization during transaction commits.<br>\*   USER I/O: I/O caused by user threads, such as I/O waits from reading data blocks.<br>\*   SYSTEM I/O: Waits caused by I/O from database background threads, such as I/O waits from the Database Writer thread synchronizing dirty blocks to disk.<br>\*   OTHER: Other types of waits.<br>\*   IDLE: The database is waiting for client messages; this type of wait event indicates that the session is idle at this time.<br>\*   NETWORK: Waits caused by network transmission, such as waits occurring during data transmission over the network.<br>\*   CONFIGURATION: Waits caused by database instance configuration, such as waits when configuring redo size, undo size, DATA BUFFER size.<br>\*   CLUSTER: Cluster wait events, such as waits for responses during cross-node interactions.<br>\*   DISTRIBUTED: Distributed wait events, such as waits for responses from nodes by CN. |