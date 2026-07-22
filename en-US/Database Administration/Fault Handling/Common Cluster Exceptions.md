In a cluster database environment, some abnormal issues may occur. By checking the run log and comparing the actual performance of the cluster, the causes of the issues can be determined and resolved.

### 1. Database instance startup failure during cluster startup

**Possible Causes**:

When multiple servers issue instance startup commands almost simultaneously, multiple database instances may experience concurrent startups, potentially leading to internal conflicts that cause instance startup failures.

**Possible Observation Points**:

The db run log contains the log: `[AXC] trigger shutdown instance due to database is not open`.

**Mitigation Solutions**:

Avoid issuing instance startup commands from multiple servers simultaneously; when manually starting instances, log into each server in sequence and operate accordingly, ensuring that instances are started to an open state.

### 2. Database instance repeatedly disconnects and is automatically restarted during cluster operation

**Possible Causes**:

When multiple servers issue instance startup commands almost simultaneously, multiple database instances may experience concurrent startups, potentially leading to internal conflicts that cause instance startup failures.

**Possible Observation Points**:

- The yasdb process is repeatedly started and stopped.
- The db run log contains the log: `[AXC] trigger shutdown instance due to database is not open`.

**Mitigation Solutions**:

Manually stop the instance on one of the servers and restart the instance on another server to an open state. Once successful, start the instance on the first server to an open state.

### 3. Database instance disconnects during cluster operation and cannot be recovered

**Possible Causes**:

During the instance recovery phase, DATA BUFFER is required. At this point, the necessary page memory may not be freed, causing the recovery process to get stuck.

**Possible Observation Points**:

- Query v$instance_recovery, if the IN_RECOVERY field is true and the PHASE field remains LOCK RECOVERY SET, it indicates that the recovery process is stuck.
- Query v$buffer_pool_statistics, if the ratio of NUM_RESIDENT and NUM_WRITE to NUM_TOTAL is large, this indicates that there are fewer pages available for eviction.
- Frequently query v$dbwr_statistics; if CURRENT_STATUS field shows BUFFER CLEAN data frequently, it indicates that available BUFFER pages are too few and need frequent cleaning; or if the view data remains unchanged, it indicates that the cleaning process may be blocked by online recovery.
- Query v$sysstat, if the data for NAME field as BUFFER FREE REQUEST continues to increase, it indicates that the number of eviction requests is rising.

**Mitigation Solutions**:

Connect to the existing open state primary instance and execute ALTER SYSTEM SET DATA_BUFFER_SIZE statement to temporarily expand the DATA BUFFER. If the PHASE field of v$instance_recovery remains LOCK RECOVERY SET, continue to expand the DATA BUFFER until the field changes and the disconnected instance successfully recovers to an open state.