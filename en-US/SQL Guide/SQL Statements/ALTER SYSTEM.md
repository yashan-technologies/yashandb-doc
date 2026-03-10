General Description
----

ALTER SYSTEM is used to dynamically change the attributes of the database instance in which it resides. The changes take effect immediately (but for ALTER SYSTEM SET PARAMETER, it can be set to take effect on restart) and apply to all users.

Statement Definition
----

**alter system::=**

```ebnf+diagram
syntax::= ALTER SYSTEM (set_parameter_clause
|SWITCH LOGFILE
|CHECKPOINT
|FLUSH BUFFER_CACHE
|FLUSH SHARED_POOL
|EXTEND BUFFER_CACHE SIZE size_clause
|ARCHIVE LOG CURRENT
|kill_session_clause
|cancel_sql_clause
|IGNORE STANDBY MISMATCHED REDO
|dump_clause
|FLUSH GTS
|CLEAN RESIDUAL TABLESPACE
|CLEAN MESSAGE POOL
|LISTENER (START|STOP))
```

**[set\_parameter\_clause](#setparameterclause)::=**

```ebnf+diagram
syntax::= SET parameter_name "=" parameter_value 
[SCOPE "=" (spfile|memory|both)] 
[(TYPE "=" (CN|DN|MN|ALL)) | (NODE "=" node_id)]
```

**[kill\_session\_clause](#killsessionclause)::=**

```ebnf+diagram
syntax::= KILL SESSION "'" session_id "," session_serial "'"
```

**[cancel\_sql\_clause](#cancelsqlclause)::=**

```ebnf+diagram
syntax::= CANCEL SQL "'" session_id "," session_serial ["," sql_id] "'"
```

**[dump\_clause](#dumpclause)::=**

```ebnf+diagram
syntax::= DUMP (PRIVATE LOG
|LOGFILE file_name
|DATAFILE file_id [BLOCK block_id|MINBLOCK block_id MAXBLOCK block_id]
|SESSION sid BACKTRACE
|REDO TYPE)
```

<span id="setparameterclause" name="setparameterclause" class="yaslink"></span>

### 1. set\_parameter\_clause

This statement is used to modify the database's [configuration parameters](../../All Manuals/Reference Manual/Configuration Parameters). 
ALTER SYSTEM SET modifies configuration parameters and prints descriptive information of the changes to the RUN LOG. 
In ISC Distributed Cluster Deployment, it is still recommended to use the YCM graphical interface or the [yasboot](../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command line to modify configuration parameters.

If parameter_name is a read-only parameter as defined by YashanDB, executing this statement will prompt the error YAS-02138.

YashanDB defines the following types of configuration parameters:

*   Read-only parameters, which cannot be modified.
*   Configurable parameters, which become effective immediately after modification.
*   Configurable parameters, which require a restart of the instance to take effect after modification.

> **Note**: 
>
> In a distributed cluster, executing this statement by default modifies the current node.

#### 1.1. SCOPE

SCOPE is used to set how the changes to the configuration parameters will take effect, with a default of BOTH.

*   spfile: Writes the parameter value to disk, requiring a restart to take effect
*   memory: Writes the parameter value to memory, taking effect immediately but becoming ineffective after a restart
*   both: Writes the parameter value to both memory and disk, taking effect immediately and remaining effective after a restart

If VALUE is assigned to a parameter that YashanDB specifies as not immediately effective, SCOPE must also be specified with SPFILE, or the error YAS-06001 will be prompted.

***Example***

```sql
SHOW PARAMETER data_buffer_size;
NAME                  VALUE     
--------------------- -------   
DATA_BUFFER_SIZE      64M       
  
ALTER SYSTEM SET data_buffer_size=128M scope=spfile;
SHOW PARAMETER data_buffer_size;
NAME                  VALUE     
--------------------- -------   
DATA_BUFFER_SIZE      64M       
  
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    300     
 
  
ALTER SYSTEM SET checkpoint_timeout=400;
SHOW PARAMETER checkpoint_timeout;
NAME                  VALUE   
--------------------- ------- 
CHECKPOINT_TIMEOUT    400
```

#### 1.2. TYPE

This statement is used only in ISC Distributed Cluster Deployment to specify the type of node for configuration.

* CN: Modify configuration parameters for all CN nodes
* DN: Modify configuration parameters for all DN nodes
* MN: Modify configuration parameters for all MN nodes
* ALL: Modify configuration parameters for all nodes, including all CN, DN, and MN

If TYPE is not specified, the default is to modify the configuration parameters of the local node.

> **Note**: 
>
> 1. TYPE cannot be specified in NOMOUNT/MOUNT mode; it defaults to modifying the configuration parameters of the local node.
>
> 2. For nodes not in OPEN status, even if they fall within the TYPE specified range, their configuration parameters cannot be modified across nodes.
>
> 3. The above description pertains to the rules when executing from a connected CN node; if connected directly to other nodes, TYPE cannot be specified and defaults to modifying the configuration parameters of the local node.

***Example*** for ISC Distributed Cluster Deployment

```sql
--Modify RUN_LOG_FILE_COUNT on all CN nodes
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = CN;

--Modify RUN_LOG_FILE_COUNT on all DN nodes
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = DN;

--Modify RUN_LOG_FILE_COUNT on all MN nodes
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = MN;

--Modify RUN_LOG_FILE_COUNT on all CN, DN, and MN nodes
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH TYPE = ALL;
```

#### 1.3. NODE

This statement is used only in ISC Distributed Cluster Deployment to specify the node for configuration. node_id = g-n.
* g: Group number ID of the node
* n: Node ID, '*' indicates all nodes within the group

If NODE is not specified, the default is to modify the local node's configuration parameters.

> **Note**: 
>
> 1. NODE cannot be specified in NOMOUNT/MOUNT mode; it defaults to modifying the configuration parameters of the local node.
>
> 2. For nodes not in OPEN status, even if they fall within the NODE specified range, their configuration parameters cannot be modified across nodes.
>
> 3. The above description pertains to the rules when executing from a connected CN node; if connected directly to other nodes, NODE cannot be specified and defaults to modifying the configuration parameters of the local node.

***Example*** for ISC Distributed Cluster Deployment

```sql
--Modify RUN_LOG_FILE_COUNT on MN node 1-1
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH NODE = 1-1;

--Modify RUN_LOG_FILE_COUNT on all CN nodes
ALTER SYSTEM SET RUN_LOG_FILE_COUNT=200 SCOPE=BOTH NODE = 2-*;
```

<span id="switch_logfile" name="switch_logfile" class="yaslink"></span>

### 2. SWITCH LOGFILE

This statement is used to trigger a forced switch of the current redo log file in the database. This operation is not a trigger for archiving.

***Example***

```sql
ALTER SYSTEM SWITCH LOGFILE;
```

### 3. CHECKPOINT

This statement is used to trigger a full CHECKPOINT in the database to write dirty data in memory to disk. 

***Example***

```sql
ALTER SYSTEM CHECKPOINT;
```

### 4. FLUSH BUFFER\_CACHE

This statement is used to invalidate all Blocks in the Buffer Cache of the database. This operation clears all buffer data, causing subsequent SQL queries to perform physical reads from disk.

***Example***

```sql
ALTER SYSTEM FLUSH BUFFER_CACHE;
```

### 5. FLUSH SHARED\_POOL

This statement is used to reclaim invalid pool memory resources in the database. This operation clears SQL statement buffers that have not been used for a long time, causing subsequent identical SQL statements to be recompiled and parsed.

***Example***

```sql
ALTER SYSTEM FLUSH SHARED_POOL;
```

### 6. EXTEND BUFFER\_CACHE SIZE

This statement is used to temporarily expand the Data Buffer size of the database, becoming ineffective after a restart.

#### 6.1. size\_clause

Specifies an integer value for the Data Buffer, with units being B/K/M/G/T/P/E. The limit on the SIZE set by this statement for each execution is restricted by two values:

*   The physical memory of the server
*   2^24\*DB_BLOCK_SIZE\*DATA_BUFFER_PARTS

Where DATA_BUFFER_PARTS indicates the number of partitions of the Data Buffer, which defaults to 1. If adjustment of this value is needed, please contact our technical support.

Exceeding the limit will prompt the error YAS-00101.

***Example***

```sql
ALTER SYSTEM EXTEND BUFFER_CACHE SIZE 4G;
```

<span id="archive_logfile" name="archive_logfile" class="yaslink"></span>

### 7. ARCHIVE LOG CURRENT

This statement is used to trigger a switch and archive of the current redo log file in the database. This operation requires the database's archiving mode to be enabled; otherwise, it will prompt the error YAS-02079.

***Example***

```sql
ALTER SYSTEM ARCHIVE LOG CURRENT;
```

<span id="killsessionclause" name="killsessionclause" class="yaslink"></span>

### 8. kill\_session\_clause

This statement is used to terminate a specified session and roll back any uncommitted transactions in that session, releasing the locks generated by that session. Afterward, running SQL statements in that session will prompt a connection error message.

Executing this command requires that the session SID and SERIAL# be provided.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Under the sales user, execute the following statement
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01' FOR UPDATE;
  
-- View lock and session information
SELECT SID,ID1,ID2,LMODE,REQUEST FROM V$LOCK;
    SID             ID1        ID2 LMODE     REQUEST   
-------- --------------- ---------- --------- ---------
      21            1328            TS                 
      21     21474844928          0 ROW                
  
SELECT sid,serial# FROM V$SESSION WHERE sid=21;
     SID      SERIAL#
-------- ------------
      21            2
          
  
-- The following operation requires administrative privilege 
-- Terminate the specified session using SID and SERIAL#
ALTER SYSTEM KILL SESSION '21,2';
```

**Cross-CN Session Termination in a Distributed Environment**

In ISC Distributed Cluster Deployment configured with multiple CNs, this statement can be used to terminate specified client connection sessions across CNs, supported only by using the GLOBAL_SESSION_ID to specify the corresponding session.

GLOBAL_SESSION_ID (the global session ID in ISC Distributed Cluster Deployment) can be obtained through the USERENV function or by querying the GV$SESSION view. In the set of sessions with the same GLOBAL_SESSION_ID, only one session connects the client to the server CN node, while the others are internal sessions between the server nodes (whose lifecycle is synchronized with the client connection session). For more details on session content, refer to [Session Management](../../All Manuals/数据库管理/运行监控/会话管理).

***Example*** (in ISC Distributed Cluster Deployment configured with CN nodes 2-1 and 2-2)

```sql
-- Connect to 2-1, establish a session, and check the global session ID of this session
-- Use GSID parameter when querying with USERENV function
SELECT USERENV('GSID') FROM dual;
USERENV('GSID') 
-------------- 
        131091

-- Get the session identifier for the session connected to the specified CN node; CN nodes are uniquely identified by group_id and group_node_id
SELECT global_session_id, serial# 
FROM GV$SESSION WHERE global_session_id IN (131091) AND group_id=2 AND group_node_id=1;
GLOBAL_SESSION_ID      SERIAL# 
----------------- ------------ 
           131091            5
           
-- Connect to 2-2, do not close the session on 2-1, and terminate the session on 2-1 from 2-2
ALTER SYSTEM KILL SESSION '131091,5';
```

<span id="cancelsqlclause" name="cancelsqlclause" class="yaslink"></span>

### 9. cancel\_sql\_clause

This statement is used to terminate an ongoing SQL operation, requiring the SID and SERIAL# of the session where the SQL operation resides.

Specifying SQL_ID indicates terminating the SQL operation corresponding to that SQL_ID; if not specified, it indicates terminating the currently running SQL operation in that session.

In a distributed environment, it supports cross-CN termination of SQL operations, requiring the session's GSID and SERIAL# to be provided.

***Example*** for Standalone/YAC/Distributed Cluster Deployment: Session 1—Under the sales user, execute the following statement:

```sql
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01' FOR UPDATE;
```
***Example*** for Standalone/YAC/Distributed Cluster Deployment: Session 2—Under the sales user, execute the following statement:

```sql
-- Open a new session 2, log in with the sales user, add locking SQL to keep it executing
UPDATE area SET DHQ='Shanghai3' WHERE area_no='01';
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment: Session 3—Under a DBA privilege user, execute the following statement:

```sql
-- Open a new session 3 and log in with DBA privileges
SELECT SID,ID1,ID2,LMODE,REQUEST FROM V$LOCK WHERE request='ROW';
     SID            ID1       ID2 LMODE     REQUEST 
-------- -------------- --------- --------- ---------
      21     4447535124                     ROW     
  
-- Query SQL_ID
SELECT sid,serial#,sql_id FROM V$SESSION WHERE sid=21;
    SID      SERIAL#                SQL_ID
-------- ------------ ---------------------
      21            1             3829447373       
  
-- Terminate the specified SQL
ALTER SYSTEM CANCEL SQL '21,1,3829447373';

-- Terminate the currently running SQL
ALTER SYSTEM CANCEL SQL '21,1';
```

In ISC Distributed Cluster Deployment, Sessions 1 and 2 are executed on cn2-1, while Session 3 operates on cn2-2, allowing SQL termination across CNs.

### 10. IGNORE STANDBY MISMATCHED REDO

This statement is used to ignore mismatched redo logs between the standby database and the primary database, allowing the standby database to continue receiving primary database logs from the redo log divergence point. This operation requires the standby database to be in REDO MISMATCH state, where it cannot receive primary database logs.

***Example***

```sql
ALTER SYSTEM IGNORE STANDBY MISMATCHED REDO;
```

<span id="dumpclause" name="dumpclause" class="yaslink"></span>

### 11. dump\_clause

This statement is used to dump internal information of YashanDB to trace files for users to analyze and determine faults.

The generated trace files are located in the database's diag/trace directory, the location of which can be configured via the DIAGNOSTIC_DEST parameter (defaults to the {YASDB_DATA} directory).

**dump**

In the dump operation, users specify a type of internal information, and the current session's information of that type is dumped into the trace file.

Each dump operation generates a segment of information in the trace file, and multiple dump operations can be executed within a session.

**trace**

The trace file is created upon the first execution of dump in the session, stored in the diag/trace directory, with a filename format of yashandb_{yyyymmdd}_{sid}.trc.

After the trace file is created, all subsequent dumped information in that session is written to the file.

When the session ID is reused, the trace file corresponding to the sid will also be reused, and no new trace file will be created.

> **Note**: 
>
> When the object being dumped is of redo type, the trace file is fixed to "yas_redo_type.trc".

#### 11.1. PRIVATE LOG

Dump the private log information of the current session into the trace file. This type of dump requires the database to be in mount or open state.

#### 11.2. LOGFILE

Dump the page information of a specific log file into the trace file. This type of dump requires the database to be in mount or open state.

This operation will lock the log file; if multiple sessions dump the same log file simultaneously, lock waiting will occur.

When dumping certain groups of a log file about encrypted tablespaces, these groups will be protected and not parsed.

**file_name**

Log name, which must exist in the V$LOGFILE view.

#### 11.3. DATAFILE

Dump the page information of a specific data file into the trace file. This type of dump requires the database to be in mount or open state.

This operation will lock the data file; if multiple sessions dump the same data file simultaneously, lock waiting will occur.

When dumping certain pages of an encrypted tablespace's data file or entire data file, those pages will be protected and not parsed.

When pages exist in memory, it will dump both memory information and page information; otherwise, it will dump only page information.

**file_id**

Specifies the ID of the data file, which is the global ID of the data file, meaning its ID field value in the V$DATAFILE view.

**block_id**

Specifies the page ID within the data file:

- Not specifying BLOCK: dump all pages.
- BLOCK block_id: specify a single page.
- MINBLOCK block_id [MAXBLOCK block_id]: specify a range of pages. Both MINBLOCK and MAXBLOCK must be specified; otherwise, an error will be reported.
- When specifying a range of pages, if MAXBLOCK is less than MINBLOCK or MAXBLOCK exceeds the file size, an error will be reported.

#### 11.4. SESSION BACKTRACE

Dump the current stack information of the specified session into the trace file. This type of dump requires the database to be in mount or open state.

This operation does not allow concurrency; only one session can perform this type of dump at a time.

#### 11.5. REDO TYPE

Dump all redo type information of the database into the trace file. The trace file name will be "yas_redo_type.trc". In the redo type information, the Id column indicates the redo type identifier, the Type column indicates the type name, and the Size column indicates the length of that redo type; if the length is -1, it indicates variable-length redo.

***Example***

```sql
ALTER SYSTEM DUMP PRIVATE LOG;

ALTER SYSTEM DUMP LOGFILE 'redo1';

ALTER SYSTEM DUMP DATAFILE 6;

ALTER SYSTEM DUMP DATAFILE 6 BLOCK 0;

ALTER SYSTEM DUMP DATAFILE 6 MINBLOCK 128 MAXBLOCK 137;

ALTER SYSTEM DUMP SESSION 23 BACKTRACE;

ALTER SYSTEM DUMP REDO TYPE;
```

### 12. FLUSH GTS

This statement is used to forcefully synchronize the SCN of the GTS service to all CN nodes. It cannot be used in Standalone Deployment. In ISC Distributed Cluster Deployment, only CN nodes can use this statement.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER SYSTEM FLUSH GTS;
```

### 13. CLEAN RESIDUAL TABLESPACE

This statement is used to manually clean up the tablespace residual after migrating chunks, and can only be used in ISC Distributed Cluster Deployment.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER SYSTEM CLEAN RESIDUAL TABLESPACE;
```

### 14. CLEAN MESSAGE POOL

This statement is used to manually clean up message pool and can only be used in YAC/Distributed Cluster Deployment.

***Example*** for YAC/Distributed Cluster Deployment

```sql
ALTER SYSTEM CLEAN MESSAGE POOL;
```

### 15. LISTENER

This statement is used to adjust the operating mode of the database connection listener. Without shutting down the database, it can control whether to accept new connections as needed. Defaults to normal operation mode.

- START: Sets the connection listener to normal operation mode, in which new connections are normally accepted.

- STOP: Sets the connection listener into maintenance mode. In this mode, it maintains existing connections but blocks new connections, permitting only local UDS connections initiated by the SYS user to access the database until it is switched back to normal operation mode or the database is restarted.

***Example***

```sql
ALTER SYSTEM LISTENER STOP;


ALTER SYSTEM LISTENER START;
```
