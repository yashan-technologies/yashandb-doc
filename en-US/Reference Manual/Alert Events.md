YashanDB implements an alarm mechanism. When the database encounters specific conditions, it reports alarm events in the alarm log. When the condition is resolved, the alarm event is cleared. There are also some alarm events that only need to be reported and do not require clearing, such as deadlocks, which are generally caused by business logic rather than internal database errors. After manual intervention to resolve the issue, the system reports the alarm and does not need to further track this event.

This document will list all alarm event information for YashanDB and provide handling suggestions upon encountering each alarm event.

**Alphabetical Index**

[a](#aalarm)		[c](#calarm)		[d](#dalarm)		[i](#ialarm)		[l](#lalarm)		[m](#malarm)		[s](#salarm)		[v](#valarm)

<span id="aalarm" name="aalarm"></span>

### AllocatorExhausted

An alarm is reported when memory usage exceeds 80%, and it is cleared when it falls below 70%.

This alarm event needs to be cleared.

Handling Suggestions:

- Query the view V$ALLOCATOR. If MAX_MEMORY_USED equals TOTAL_MEMORY, it indicates that the memory for the corresponding memory allocator has been exhausted.
- If there is insufficient virtual memory in the columnar cache, increase COLUMNAR_VM_BUFFER_SIZE or COLUMNAR_BUFFER_SIZE, or decrease COLUMNAR_DATA_BUFFER_PERCENT or COLUMNAR_BULK_SIZE.

### AnalyzeDataForward

When collecting statistics fails to sync with other CNs, it logs an alarm.

This alarm event is not cleared.

Handling Suggestions:

- Check if the network connection is normal.
- Check if other CN nodes are functioning properly.
- Re-execute statistics collection.

### AuditRecordTableFull

When executing the CHECK_AUDIT_THRESHOLD function, if the return value exceeds 80, it indicates that the size of the audit records table has surpassed 80% of the SYSAUX tablespace's maximum capacity, and an alert log will be recorded.

This alarm event is not cleared.

Handling Suggestions:

- Back up the contents of the audit records table, and use the DBMS_AUDIT_MGMT advanced package to delete unnecessary audit records from the audit records table.
- Execute the CHECK_AUDIT_THRESHOLD function, or set AUDIT_RECORD_TABLE = TRUE to switch the audit record storage target back to the audit records table.

<span id="calarm" name="calarm"></span>

### ConditionFailover

When the primary database triggers a condition for failover, it will immediately shut down (terminate YASDB process) to automatically elect a new primary. The primary database will log an alarm before shutting down.

This alarm event is not cleared.

Handling Suggestions: Check the Failover conditions triggered by the primary database, fix them, restart, and determine whether to execute a switchover based on business needs.

### CursorExhausted

An alarm is reported when a cursor cannot be allocated. The alarm is cleared when the available cursors in the cursor pool reach 30% or above.

This alarm event needs to be cleared.

Handling Suggestions: Check if the business concurrency is too high.

<span id="dalarm" name="dalarm"></span>

### DatabaseForceExit

In Maximize protection mode, if there is an exception in synchronizing the standby database and a shutdown abort is executed, it may result in the buffer being too small (DATA_BUFFER_SIZE configuration too small), preventing normal execution of checkpoint, and shutdown abort getting stuck. When the database detects this condition, it will force exit and report an alarm. 

In Maximize protection mode, if there is an exception in synchronizing the standby database and transactions cannot be committed, executing a shutdown immediate will cause the database to wait for transaction commits and after a period, force exit and report an alarm. 

In YAC/Distributed Cluster Deployment, when YCS instances and database instances are kicked out of the cluster, it will wait for in-flight IO to finish before force exiting, to avoid data corruption, and report an alarm.

This alarm event is not cleared.

Handling Suggestions:

- Check the reason for the exception in synchronizing the standby database, fix the exception in the standby database or change the standby database.
- If the shutdown abort getting stuck is caused by the DATA_BUFFER_SIZE being too small, adjust this parameter after a restart.
- In a YAC scenario, check for disk and network faults and use the *ycsctl* tool to check whether the cluster topology is correct.

### DatabaseIsAbnormal

When the database encounters a fault, it is set to an abnormal state, and an alarm is reported.

This alarm event is not cleared.

Handling Suggestions:

- Check if the standby node is functioning properly.
- Check if the database data files have been deleted or are abnormal.
- Check if the database redo files have been deleted or are abnormal.
- Check if there is enough disk space.

### DataBucketExhausted

When obtaining the available databuckets under the corresponding tablespace during LSC table conversion fails (bucket corresponds to insufficient disk space), an alarm for bucket resource exhaustion is reported. The alarm is cleared when the bucket space falls below the threshold and becomes available.

This alarm event needs to be cleared.

Handling Suggestions:

- Check if the disk space corresponding to the databucket is sufficient; if not, increase the databucket or clean up unused LSC tables to release table space.
- Check the configuration item BUCKET_RESERVED_SPACE, which indicates how much disk space must be reserved on the disk where the databucket is located to avoid being written into. If it is set too high, it can be appropriately reduced.

### DeadLock

When any of the three types of deadlocks occur, the database will select one session in the deadlock cycle to return an error and log an alarm.

- Transaction deadlock: Concurrent waiting occurs when transactions modify the same data, which can cause deadlock.
- Xslot deadlock: When there are insufficient transaction slot (Xslot) resources within a page, transaction waiting can occur, possibly leading to deadlock.
- Table lock deadlock: When transactions add shared and exclusive locks to a table, transaction waiting can occur, possibly leading to deadlock.

This alarm event is not cleared.

Handling Suggestions:

- Actively release the transactions waiting in the deadlock.
- Investigate the business that caused the deadlock and adjust it to avoid further deadlocks.

### DistributeNodeDisconnect

An alarm is reported when an exception occurs in a distributed node. The alarm is cleared after the node recovers.

This alarm event needs to be cleared.

Handling Suggestions:

- Check if the network connection between distributed nodes is normal.
- Check if any distributed node processes have exited.

<span id="ealarm" name="ealarm"></span>

### ExceedIdleTime

If a session is idle for longer than the maximum idle time set for the user, the server will disconnect that session.

This alarm event is not cleared.

Handling Suggestions:

- Sessions should not be idle for too long. If the database is not needed, disconnect the connection to avoid resource occupation.
- If a long-term connection is required, modify the IDLE_TIME configuration of the corresponding user profile to adjust the maximum idle time.

<span id="ialarm" name="ialarm"></span>

### Incident

When event records exceed 5 times in one hour or 25 times in one day, a flood control alarm is recorded.

This alarm event is not cleared.

Handling Suggestions: Check the alarm events and clear the related alarms.

<span id="lalarm" name="lalarm"></span>

### LicenseWillExpired

The license is set to expire within thirty days.

This alarm event does not require clearing.

Handling Suggestions: Apply for a new license to update the soon-to-expire license.

### LockExhausted

An alarm is reported when a lock cannot be allocated. The alarm is cleared when the available resources in the lock area reach 30% or above.

This alarm event needs to be cleared.

Handling Suggestions: Check if the business concurrency is too high.

<span id="malarm" name="malarm"></span>

### MemoryPoolExhausted

An alarm is reported when the memory pool is full. The alarm is cleared when the available memory in the memory pool reaches 30% or above.

This alarm event needs to be cleared.

Handling Suggestions:

- Check if the corresponding MEMPOOL size is appropriate; if not, increase it.
- Check if the business concurrency is too high.

### RsrcDropActivePlan

When deleting a non-MN primary node's effective resource plan under ISC Distributed Cluster Deployment, an alarm is reported for deleting the effective plan.

This alarm event does not require clearing.

Handling Suggestions: Use the *yasboot* tool to modify the configuration parameter RESOURCE_MANAGER_PLAN to keep it globally consistent.

### RsrcPlanNotExists

When the resource manager plan specified by the configuration parameter RESOURCE_MANAGER_PLAN does not exist in the database, an alarm for resource plan non-existence is reported. The alarm is cleared upon creation of the corresponding resource plan.

This alarm event needs to be cleared.

Handling Suggestions: Use an existing plan in the system.

<span id="salarm" name="salarm"></span>

### SessionExhausted

An alarm is reported when the number of connections reaches its limit. The alarm is cleared when the available connections reach 30% or above.

This alarm event needs to be cleared.

Handling Suggestions:

- Check if the size of the configuration item MAX_SESSIONS is appropriate; if not, increase it.
- Check the active connections through V$SESSION to determine if the business concurrency is too high.
- After reserving connections, kill off long-running idle sessions.

### SpaceFull

When a tablespace is full and no pages can be allocated, an alarm log is recorded.

This alarm event is not cleared.

Handling Suggestions:

- Expand the tablespace.
- Delete unused objects in the tablespace or perform a shrink operation on sparse tables to release space.

### SqlPoolFull

When the SQL pool usage reaches 90%, an alarm log is recorded.

When usage drops to 50%, the alarm is automatically cleared.

Handling Suggestions: Release unused SQL statements in a timely manner.

### StandbyDisconnect

When the primary cluster detects a disconnection from the standby cluster, an alarm log is recorded. When the primary cluster detects that the standby cluster has reconnected, it logs the clearing of the alarm.

This alarm event needs to be cleared.

Handling Suggestions:

- Check if the network connection between primary and standby is normal.
- Check if the standby database has exited.

<span id="valarm" name="valarm"></span>

### vgdFull

When LSC tables are equipped with Delta storage, data may be temporarily placed in Delta storage when a small amount of data is inserted. When a threshold is reached or certain conditions or operations are satisfied, it is consolidated into SCOL files. An alarm for insufficient insert buffer is reported when the Delta storage is full. The alarm is cleared when one or more Deltas have space that can be inserted into the Delta storage.

This alarm event needs to be cleared.

Handling Suggestions:

- Check the TABXFMR$ system table to see if the conversion task is complete.
- Check if there are any unfinished transactions on LSC tables. Unfinished transactions will block the conversion tasks from Delta storage to SCOL format.

### YcsDbFenced

In a YAC, the current database instance and YCS instance being kicked out of the cluster require waiting for in-flight IOs to finish before forcibly stopping the database service.

This alarm event needs to be cleared.

Handling Suggestions:

- Use the *ycsctl* tool to check if the cluster configuration and status are normal.
- After excluding network and disk faults, restart the YCS and database instances to rejoin the cluster. Successful reintegration will clear the alarm.