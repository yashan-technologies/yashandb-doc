The alarm log does not have a switch control and is always enabled, which means it continuously keeps a record file. The size of the record file has no upper limit, and when the alert.log file becomes too large, it needs to be manually cleared.

This document will introduce common alarm examples in YashanDB.

## Tablespace Full

space full: When the tablespace is full and no pages can be allocated, an alarm log is recorded.

When a certain tablespace is fully utilized, the alarm log will produce an error message.

***Example***

```verilog
//Session 24 detected on 2022-06-20 00:20:46 that the usage of tablespace TBS_TPCC has reached 100%, triggering the SpaceFull alarm event.
//53 indicates the space id of the tablespace, and 0 indicates that the alarm has not been cleared. "tablespace TBS_TPCC is full" is the alert message.
2022-06-20 00:20:46.514|22|SpaceFull|53|0||tablespace TBS_TPCC is full
2022-06-20 00:20:46.520|24|SpaceFull|53|0||tablespace TBS_TPCC is full
```

## Connection Count Full

session exhausted: An alarm is reported when the connection count is full. The alarm is cleared when the available connection count reaches 30% or more.

When the current session connection count exceeds the maximum value set by the MAX_SESSIONS parameter, the log will produce an error message.

***Example***

```verilog
2022-06-19 22:45:46.542 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
2022-06-19 22:45:46.554 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
2022-06-19 22:45:46.554 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
2022-06-19 22:45:46.554 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
2022-06-19 22:45:46.554 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
2022-06-19 22:45:46.554 5367 [ERROR] alloc session failed, error code:6013, error message: too many sql handlers
```

At this time, the client cannot connect to the database and prompts:

```sql
YAS-00406 connection is closed
YASQL-00007 invalid username/password; logon denied
```

## Primary/Standby Connection Disconnected

standby disconnect: When the primary database detects that the standby database is disconnected, an alarm log is recorded. When the primary database detects that the standby database is reconnected, an alarm clearing log is recorded.

When the primary database and standby database are disconnected, the alert.log prints as follows:

```verilog
//On 2021-10-29 19:41:05.841, session 18 detected that the primary database found the standby database disconnected in the current HA architecture, and the primary database failed to transmit logs to the standby database, triggering the StandbyDisconnect alarm event.
//1 indicates the standby database ID, and 0 indicates that the alarm is not cleared. "redo sender failed to connect" is the alert message.
2021-10-29 19:41:05.841|18|StandbyDisconnect|1|0||redo sender failed to connect with standby 
```

When the primary database and standby database are reconnected, the alert.log prints as follows:

```verilog
//On 2021-10-29 19:41:23.877, the primary database and standby database reconnected, clearing the disconnection alarm.
//The first 1 indicates the standby database ID, and the second 1 indicates that the alarm is cleared.
2021-10-29 19:41:23.877|18|StandbyDisconnect|1|1|
```

## Deadlock Occurred

dead lock: When user operations improperly lead to deadlock situations in the database, the storage engine can detect the deadlock issue by terminating the wait for a session in the deadlock cycle, thus resolving the deadlock state.

When any of the following three types of deadlocks occur, the database selects one session from the deadlock cycle to return an error, generates a corresponding deadlock trace log, and records an alarm log indicating the trace log file path:

- Transaction deadlock: Concurrent updates to the same data by transactions can lead to deadlocks.

- Xslot deadlock: Insufficient resources in the transaction slot (Xslot) within a page can cause transaction waits, potentially leading to deadlocks.

- Table lock deadlock: Transactions acquiring shared locks and exclusive locks on tables can cause transaction waits, potentially leading to deadlocks.

For example, when a deadlock occurs due to mutual updates between transactions, the alert.log prints as follows:

```verilog
//Session 19 detected a transaction deadlock, triggering the DeadLock alarm event.
//21 is the ID of this deadlock, 0 indicates the status of the alarm has not been cleared; lockType indicates the type of lock generated, "found xact dead lock" is the alert message.
2022-06-23 18:44:17.191|19|DeadLock|21|0|lockType=2|found xact dead lock, more info in xxx.trc (xxx.trc is the specific deadlock trace log path)
```
