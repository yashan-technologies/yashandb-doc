If issues arise during the primary/standby operation in a high-availability environment, the root cause can be located by checking the V$REPLICATION_EVENT, V$DIAG_INCIDENT views, and the run log.

<span id="primarybusstuck" name="primarybusstuck" class="yaslink"></span>

### Primary Database Business Stuck

Possible reasons include:

- The archive disk is full, and log switching cannot occur. At this time, you can execute SQL commands to clean up unnecessary archive files or set the archive automatic cleanup parameters.

- In maximize protection mode, if the standby database encounters an exception, the transaction redo cannot be synchronized to the standby database. In this case, you can start or repair the standby database and wait for the standby database to synchronize the redo.

- In maximize protection mode, if the standby database encounters an exception, the redo of the transaction that synchronously writes the audit log on the primary database cannot be synchronized to the standby database, or the buffer for asynchronously writing the audit log on the primary database is full, causing a block. You can disable auditing, repair the database, and then re-enable auditing.

### Primary Database Executes Business and Reports Read Only Error, v$database Status Changes to ABNORMAL

Possible reasons include:

- Insufficient archive disk space causes archiving to fail. In this case, you can execute SQL commands to clean up unnecessary archive files or set the archive automatic cleanup parameters, and then execute alter database convert to normal.

- In maximize protection mode, if the standby database encounters an exception, the transaction redo cannot be synchronized to the standby database. In this case, you can start or repair the standby database, ensure the standby database is in a normal state, and then execute alter database convert to normal.

### Standby Database Redo Apply Slow

Possible reasons include:

- High concurrency on the primary database and the standby database has not set the parallel apply parameters. In this case, you can set the standby database parallel apply parameters and restart the standby database.

### Standby Database NEED REPAIR

This error requires checking the run log for more detailed error information.

Possible reasons include:

- The primary/standby binary versions do not match, requiring a version change.

- The primary/standby database_id is inconsistent, typically because the primary database has been rebuilt without rebuilding the standby database, leading to a mismatch.

- The primary database has cleaned up archive files, but the standby database has not received this portion of the logs, resulting in an irrecoverable gap. Build the standby database again to resolve this fault.

- The primary/standby logs do not match, possibly due to a failover event where the new primary database has lost some logs. Build the standby database again to resolve this fault.

- After the old primary database became the standby database, a split-brain condition occurred. Build the old primary database again to resolve this fault.

- In the majority standby database within the MN group (n/2, where n is the number of nodes), the data files have been cleaned up, requiring the main MN mode to switch to maximize availability mode, rebuild the corrupted standby database, and then switch the main MN back to maximize protection mode.

YashanDB provides repair measures when the NEED REPAIR status occurs; for specific operations, please refer to [Repair Abnormal Standby Database](./​​Standby Repair).

### Primary Database Shutdown Stuck

Possible reasons include:

- The shutdown mode is normal, and there are still active client connections. The normal mode waits for client connections to exit normally, requiring the user to manually exit client connections.

- The shutdown mode is not abort, and the primary database business is stuck, making it impossible for the business thread to terminate; please refer to [Primary Database Business Stuck](#primarybusstuck) for handling.

### Shutdown Abort Reports Disconnection Error, Process Exits

In maximize protection mode, if the standby database encounters an anomaly, dirty page flushing will be blocked to prevent uncommitted data from being written to disk.

When executing shutdown abort, if a thread must wait for dirty page flushing, it will not be able to terminate that thread. Therefore, after a timeout of 1 minute, the yasdb process will be forcibly terminated, and the client will report a disconnection error.

Possible scenarios include:

- When executing shutdown abort with a full data buffer, if a thread needs to request a page, it will need to evict a page from the data buffer, but if dirty page flushing is blocked, the thread cannot request a page.

- Executing tablespace-related operations may trigger a full checkpoint, but if dirty page flushing is blocked, the thread cannot wait for the checkpoint to complete.

- If redo log tailing requires a full checkpoint to release redo space, but if dirty page flushing is blocked, the redo space cannot be released, and all threads waiting for the redo space to be released cannot terminate.

### Shutdown Immediate Stops Node Stuck

When the data buffer is full, executing shutdown immediate to stop the node may become stuck.

Solution: You can execute yasboot cluster/group/node stop and specify the -f parameter to force stop the database or node.

### Stuck during Build or Parallel Build

When the primary/standby is in maximize protection mode, if all connections to the standby database are lost, the primary database transactions cannot be committed, and executing build or parallel build may occasionally become stuck.

Solution: Manually switch to maximize availability mode, and once the build is completed, you may consider switching back to another protection mode.