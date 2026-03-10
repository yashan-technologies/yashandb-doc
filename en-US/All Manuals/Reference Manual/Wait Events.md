In YashanDB, views such as V$SYSTEM_EVENT/GV$SYSTEM_EVENT/V$SESSION_WAIT display information on the current waiting events of the system. These views allow you to identify the activities that each session is waiting on, which assists in analyzing database performance bottlenecks.

This article will list and explain all the waiting events in YashanDB, as well as the conditions that trigger these waiting events.

**Alphabetical Index**

[a](#await)     [b](#bwait)		[c](#cwait)		[d](#dwait)		[e](#ewait)		[f](#fwait)		
[g](#gwait)		[h](#hwait)		[i](#iwait)		[k](#kwait)		[l](#lwait)		[m](#mwait)		
[q](#qwait)		[r](#rwait)		[s](#swait)		[t](#twait)		[u](#uwait)		[w](#wwait)		
[x](#xwait)

<span id="await" name="await" class="yaslink"></span>

### alloc execution quota 

Description: Pre-allocate execution resource quota for DML.

Trigger Condition: During the preparation phase of DML, necessary resource quota requests are made.

### alloc gc pool wait

Description: Wait for allocation of a GC resource buffer pool. This buffer pool is located in the shared pool, and increasing the shared pool memory can reduce the time spent on this wait event.

Trigger Condition: Waiting for global resource allocation buffer pool memory.

### apply datafile operation

Description: Waiting for standby database to apply tablespace and data file type operations.

Trigger Condition: Lock wait occurs during standby database operations applying to primary database's REDO that includes data file additions or deletions, while a backup is in progress and not yet completed, blocking standby database's REDO apply.

### assm block range formating

Description: Block range initialization wait.

Trigger Condition: Concurrently inserting data initializes block ranges; if two sessions are allocated to the same range, the later allocated session must wait for the initialization to finish.

### assm block range queue wait

Description: Queue wait for initializing block ranges.

Trigger Condition: Concurrently inserting data triggers block range initialization; multiple threads initializing different block ranges on the same segment fills the queue, causing later threads to wait for others to complete.

### audit policy

Description: Constrained audit policy concurrency control.

Trigger Conditions:

1. Deleting a role that is utilized in the audit policy.
2. Assigning privilege to that role while it's utilized in the audit policy.
3. Revoking privilege from that role while it's utilized in the audit policy.
4. Deleting the policy.
5. Enabling the policy.
6. Disabling the policy.
7. Modifying the policy.
8. Deleting an object that is being audited.
9. Deleting a user that is being audited.
### axl recv

Description: Wait for response events during interaction between executing across servers in a YAC.

Trigger Condition: Occurs when executing cross-server queries in YAC, such as GV views, while control messages are sent between servers.

<span id="bwait" name="bwait" class="yaslink"></span>

### btree cache invalid

Description: A YAC waiting event for concurrent index buffer invalidation.

Trigger Condition: Wait occurs when YAC fails to obtain a lock during the invalidation of hot pages in the index buffer.

### buffer busy wait

Description: Block lock conflict waiting.

Trigger Condition: Waiting occurs when modifying the same block concurrently.

<span id="cwait" name="cwait" class="yaslink"></span>

### check xa end

Description: Waiting for the second phase commit of distributed transactions.

Trigger Condition: This should not occur under normal circumstances, but may exist under exceptional circumstances (e.g., CN or related DN network anomalies, DN failures, etc.).

### checkpoint completed

Description: Checkpoint waiting.

Trigger Condition: Waiting occurs when a checkpoint is not yet completed, such as during shutdown or when a checkpoint is triggered through SQL.

### columnar ac build wait

Description: Waiting for AC data generation.

Trigger Condition: Waiting occurs when there are unfinished AC data generation tasks for the current table.

### columnar data block extending

Description: Waiting for data page extension.

Trigger Condition: Waiting occurs during data page extension for TAC tables.

### columnar data block filling

Description: Waiting for data page initialization.

Trigger Condition: Waiting occurs when initializing new column data pages and other sessions are also initializing pages.

### columnar meta block extending

Description: Waiting for metadata page extension.

Trigger Condition: Waiting occurs during metadata page extension for TAC.

### columnar meta block xact wait

Description: Waiting for a transaction management unit application in a metadata block.

Trigger Condition: Waiting occurs when there is not enough space available to apply a transaction management unit in the block.

### columnar xfmr wait

Description: Waiting for LSC mutable data conversion.

Trigger Condition: Waiting occurs when there are unfinished conversion tasks for the current table.

### connecting remote node

Description: The local node is attempting to establish a connection with other nodes.

Trigger Condition: If the execution involves nodes that do not have established connections, a connection must be established first, triggering this wait.

### ctrl file

Description: Control file lock conflict waiting.

Trigger Condition: Waiting occurs when modifying the control file concurrently.

<span id="dwait" name="dwait" class="yaslink"></span>

### db file parallel read

Description: Block asynchronous pre-read waiting.

Trigger Condition: Waiting occurs during asynchronous pre-reading of pages during standby database apply.

### db file parallel write

Description: Block flush waiting.

Trigger Condition: Waiting occurs when the database writer thread is flushing blocks.

### db file scattered read

Description: Block read waiting.

Trigger Condition: Waiting occurs when accessing a block triggers block read.

### db file sequential read

Description: Block pre-read waiting.

Trigger Condition: Waiting occurs when accessing a block triggers block pre-read.

### deadlock detection wait

Description: Waiting while deadlock detection is being performed.

Trigger Condition: Waiting occurs when the background thread triggers deadlock detection at intervals.

### dstb alter session

Description: CN is waiting for other nodes to complete alter session operations.

Trigger Condition: Waiting occurs during execution of distributed alter session.

### dstb alter system

Description: CN is waiting for other nodes to complete alter system operations.

Trigger Condition: Waiting occurs during execution of distributed alter system.

### dstb commit

Description: CN is waiting for the DN involved in the transaction to complete commit.

Trigger Condition: This wait is triggered during commit execution when the transaction involves only one DN.

### dstb execute dcl

Description: CN is waiting for other nodes to complete DDL-related transactional operations.

Trigger Condition: Waiting occurs during execution of distributed DDL.

### dstb execute ddl

Description: CN is waiting for other nodes to execute DDL commands.

Trigger Condition: Waiting occurs during the execution of distributed DDL.

### dstb lob data request

Description: Distributed nodes are waiting for the requested LOB data to be sent.

Trigger Condition: This wait is triggered when one node requests LOB data from another node.

### dstb release px res

Description: CN is waiting for other nodes to finish releasing resources.

Trigger Condition: Waiting occurs during global resource release after failure to request parallel execution resources in a distributed environment.

### dstb release savepoint

Description: CN is waiting for other nodes to complete the deletion of savepoints.

Trigger Condition: Waiting occurs when executing the release savepoint statement.

### dstb rollback

Description: CN is waiting for other nodes to complete rollback.

Trigger Condition: This wait is triggered during various rollback operations in distributed environments.

### dstb savepoint

Description: CN is waiting for other nodes to complete savepoint.

Trigger Condition: Waiting occurs when executing savepoint statements.

<span id="ewait" name="ewait" class="yaslink"></span>

### exclusive lock wait

Description: Exclusive lock conflict waiting.

Trigger Condition: Waiting occurs when Transaction A acquires a shared lock on a table, and Transaction B attempts to acquire an exclusive lock on the same table.

### extending data file

Description: Waiting for data file extension.

Trigger Condition: This wait occurs when triggering a data file extension or resizing a data file that requires extension.

<span id="fwait" name="fwait" class="yaslink"></span>

### flashback db cluster lock

Description: Waiting during full-database flashback operations in a YAC when acquiring locks.

Trigger Condition: Triggering full-database flashback-related operations in a YAC.

### flashback db free space

Description: Waiting during the resource cleanup for full database flashback.

Trigger Condition: This wait is triggered when resource usage exceeds a threshold during full database flashback.

### flashback db read data

Description: Waiting for reading flashback logs during full database flashback.

Trigger Condition: Waiting occurs while scanning flashback logs during full database flashback.

### flashback db wait fb log flush

Description: Waiting for flashback logs to be written to disk during page transmission in a YAC.

Trigger Condition: Page flow activation after enabling flashback in a YAC.

### flashback db wait push buffer

Description: Copying flashback logs to the flashback dadabase part buffer during log generation, with waiting triggered when buffer space exceeds thresholds.

Trigger Condition: Triggered during concurrent multi-threaded flashback log generation.

### flashback db wait push slot

Description: Copying flashback logs to the flashback dadabase part buffer during log generation, with waiting triggered when the number of threaded exceeds thresholds.

Trigger Condition: Triggered during concurrent multi-threaded flashback log generation.

### flashback db write data

Description: Waiting for writing flashback logs during full database flashback.

Trigger Condition: This wait is triggered when the business operations generate flashback logs after enabling full database flashback functionality and writing to flashback log files.

### free buffer object wait

Description: Waiting for buffer object eviction.

Trigger Condition: Waiting occurs when accessing a block triggers buffer object eviction.

### free buffer wait

Description: Waiting for buffer eviction.

Trigger Condition: Waiting occurs when accessing a block triggers buffer eviction.

<span id="gwait" name="gwait" class="yaslink"></span>

### gc boc wait

Description: A YAC waiting event for SCN synchronization.

Trigger Condition: Waiting for SCN transmission.

### gc buffer busy acquire

Description: A YAC waiting event where the requested buffer is in a globally busy state, and the buffer is currently being requested by other sessions of this instance from remote instances.

Trigger Condition: The buffer is currently being requested from remote instances by other sessions of this instance.

### gc buffer busy release

Description: A YAC waiting event where the requested buffer is in a globally busy state, and the buffer is held by other sessions with shared or exclusive locks.

Trigger Condition: The buffer is held by other sessions with shared or exclusive locks.

### gc cr block 2 way

Description: A YAC waiting event where the requested consistent read page is transmitted from another instance in YAC, involving 2 network hops.

Trigger Condition: Requests involving 2 network hops.

### gc cr block 3 way

Description: A YAC waiting event where the requested consistent read page is transmitted from another instance in YAC, involving 3 network hops.

Trigger Condition: Requests involving 3 network hops.

### gc cr block grant

Description: A YAC waiting event where the requested consistent read page is authorized by this instance to load from disk.

Trigger Condition: Authorization from this instance to load from disk.

### gc cr block grant 2 way

Description: A YAC waiting event for the requested consistent read page, authorized by other instances to load from disk.

Trigger Condition: Authorization from other instances to load from disk.

### gc cr request

Description: A YAC waiting event for requesting consistent read pages.

Trigger Condition: Request for consistent read pages.

### gc current block 2 way

Description: A YAC waiting event where the requested latest page is transmitted from another instance in YAC, involving 2 network hops.

Trigger Condition: Requests involving 2 network hops.

### gc current block 3 way

Description: A YAC waiting event where the requested latest page is transmitted from another instance in YAC, involving 3 network hops.

Trigger Condition: Requests involving 3 network hops.

### gc current block busy

Description: A YAC waiting event where the instance is accessing a page that is held by other sessions of the same instance.

Trigger Condition: Accessing a page that is held by other sessions.

### gc current block request

Description: A YAC waiting event for requesting the latest page.

Trigger Condition: Request for the latest page.

### gc current grant

Description: A YAC waiting event where the requested latest page is authorized by this instance to load from disk.

Trigger Condition: Authorization from this instance to load from disk.

### gc current grant 2 way

Description: A YAC waiting event where the requested latest page is authorized by other instances to load from disk.

Trigger Condition: Authorization from other instances to load from disk.

### gc flush block

Description: A YAC waiting event where the owner instance is flushing data while sending the latest page.

Trigger Condition: Waiting occurs when the current redo size exceeds the expected recovery size while sending the page.

### gc invalid block

Description: A YAC waiting event where an invalid block is held by an active page of an instance.

Trigger Condition: An invalid block is held by an active page.

### gc lwlock S

Description: A YAC waiting event requesting a gls shared lock.

Trigger Condition: Request for a gls shared lock.

### gc lwlock X

Description: A YAC waiting event requesting a gls exclusive lock.

Trigger Condition: Request for a gls exclusive lock.

### gc mutex

Description: A YAC waiting event requesting a mutex lock.

Trigger Condition: Request for a mutex lock.

### gc recycle block

Description: YAC wait event, gcs lock memory reclamation.

Trigger Condition: When data block cache is evicted, gcs lock permissions need to be immediately evicted, or orphaned gcs locks are evicted by background threads at some point.

### gc recycle lock

Description: A YAC waiting event for memory reclamation with gls locks.

Trigger Condition: Request for memory allocation when local lock memory reaches its limit.

### gc send block

Description: A YAC waiting event where the owner instance sends the latest page.

Trigger Condition: Owner instance sending the latest page.

### gc upgrade block 2 way

Description: A YAC waiting event requesting a page lock upgrade, requiring other instances to invalidate the page, involving 2 network hops.

Trigger Condition: Requests involving 2 network hops.

### gc upgrade block 3 way

Description: A YAC waiting event requesting a page lock upgrade, requiring other instances to invalidate the page, involving 3 network hops.

Trigger Condition: Requests involving 3 network hops.

### gc upgrade grant

Description: A YAC waiting event with no other instances holding valid pages.

Trigger Condition: Request for page lock upgrade.

### gc upgrade request

Description: A YAC waiting event requesting page lock upgrade.

Trigger Condition: Request for page lock upgrade.

### gcs lock busy wait

Description: YAC wait event, GCS lock busy waiting.

Trigger Condition: Requesting page lock escalation.

### gcs lock free wait

Description: YAC wait event, proactive elimination and release of GCS lock.

Trigger Condition: Requesting page lock escalation.

### get remote xact info

Description: A YAC waiting event to acquire remote transaction information.

Trigger Condition: Querying transaction information.

### get remote xact status

Description: A YAC waiting event to acquire remote transaction status.

Trigger Condition: Querying transaction status.

<span id="hwait" name="hwait" class="yaslink"></span>

### hash aggr repartition inplace

Description: Memory repartition event in Hash Group.

Trigger Condition: Triggered when the required memory for Hash Group reaches a certain threshold, and the estimated distinct values are lower than the actual distinct values.

### hash aggr repartition ooc

Description: Hash Group repartition event.

Trigger Condition: Triggered when the memory required exceeds the maximum availability memory for Hash Group.

### hash aggr resize

Description: Hash Group expanding hash table size event.

Trigger Condition: Triggered when the number of inserted rows in the hash table reaches a threshold.

### hash join build hash map

Description: Event for building hash table in Hash Join.

Trigger Condition: Triggered when constructing the hash table from the build table data.

### hash join repartition

Description: Hash Join repartition event.

Trigger Condition: Triggered when the required memory for a single partition in Hash Join exceeds the maximum available memory.

### heap segment extending

Description: Wait for heap extension.

Trigger Condition: Waiting occurs during INSERT operations that trigger heap extension.

<span id="iwait" name="iwait" class="yaslink"></span>

### index block split

Description: Waiting during structural changes when searching for a key.

Trigger Condition: Waiting occurs when other sessions are modifying block structure during the search.

### index contention

Description: Index structure change conflict waiting.

Trigger Condition: Waiting occurs when inserting a key while other sessions are changing the block structure.

### interval part extend

Description: A YAC waiting event for concurrent table partition extension.

Trigger Condition: Triggered when concurrent interval partition extensions occur on the same table in YAC.

<span id="kwait" name="kwait" class="yaslink"></span>

### key xact wait

Description: Index key lock conflict waiting.

Trigger Condition: A unique index, during INSERT, finds a duplicate value being modified by another transaction that has not been committed.

<span id="lwait" name="lwait" class="yaslink"></span>

### lbac policy

Description: A YAC waiting event for constrained LBAC concurrency control.

Trigger Condition: Executing actions such as opening/closing LBAC switches, creating/deleting LBAC policies, creating/deleting LBAC tags, creating/deleting LBAC policy association tables, creating/deleting LBAC associated users in a YAC.

### load his xfmr tasks

Description: Waiting for data transformation threads to load transformation tasks.

Trigger Condition: Waiting occurs when the data transformation thread begins execution.

### log buffer space

Description: Waiting for redo cache space allocation.

Trigger Condition: Waiting occurs when a session attempts to write redo to the redo cache and finds insufficient space.

### log file parallel write

Description: Waiting for redo file flush.

Trigger Condition: Waiting occurs when redo data is being flushed from redo cache.

### log file sequential read

Description: Waiting while reading redo/archive logs during redo sending.

Trigger Condition: Waiting occurs when the redo sending thread reads redo data.

### log file single write

Description: Waiting during redo file header flush.

Trigger Condition: Waiting occurs when the redo file header is being updated, such as when adding or switching redo files.

### log file switch completion

Description: Waiting for redo switch.

Trigger Condition: Waiting occurs during redo switch.

### log file sync

Description: Waiting for redo log commit.

Trigger Condition: Waiting occurs when a transaction commits and the redo log must be written to disk during flush.

### log switch/archive

Description: Waiting for `alter system archive`.

Trigger Condition: Waiting occurs when executing `alter system archive` command while archival has not been generated.

<span id="mwait" name="mwait" class="yaslink"></span>

### mcol wait dc load

Description: Waiting for metadata buffer loading for LSC mutable data.

Trigger Condition: Waiting occurs when the metadata buffer for the current mutable data operation is not loaded.

### merge slice wait

Description: Waiting to force a merge of stable data in LSC tables to reduce file count.

Trigger Condition: Waiting occurs when the number of stable records in LSC tables, less than 128K, reaches the configured limit.

### modify encryption key

Description: Concurrent wait during primary key modification.

Trigger Condition: Triggered when multiple instances in the cluster concurrently create or activate primary keys, or concurrently create encryption objects such as encrypted tablespaces, encrypted tables, or encrypted columns.

<span id="pwait" name="pwait" class="yaslink"></span>

### profile create

Description: A YAC waiting event for creating a profile.

Trigger Condition: Concurrently creating profiles.

<span id="qwait" name="qwait" class="yaslink"></span>

### quorum standby sync

Description: Waiting occurs when the number of synchronized standby corresponding to the QUORUM_SYNC_STANDBYS parameter changes.

Trigger Condition: In maximize protection mode, when increasing standby database links while QUORUM_SYNC_STANDBYS is set to its default value, the number of synchronized standbys may increase, causing wait if the number does not meet the new value.

<span id="rwait" name="rwait" class="yaslink"></span>

### read by other session

Description: Waiting occurs when the accessed block is being loaded by other sessions.

Trigger Condition: Concurrently accessing a block that is undergoing loading by other sessions.

### rebuild slice index

Description: Waiting occurs when slice index files are being rebuilt.

Trigger Condition: Concurrent utilization requires the slice index to be rebuilt.

### recovery read

Description: Waiting while reading redo/archive logs during redo apply.

Trigger Condition: Waiting occurs when recovery reads logs from redo/archive logs.

### redo global lock

Description: Waiting for the redo global lock.

Trigger Condition: Mutual exclusion across instances in the cluster, waiting for other instances to complete additions or deletions to redo files.

### redo receive dependency

Description: Waiting occurs when the standby database receives redo from an instance that depends on the redo from another instance that does not exist.

Trigger Condition: In YAC mode, the standby database receives redo from multiple instances, and if the dependent redo has not arrived, it will wait.

### redo reload lock

Description: Waiting to reload the redo head.

Trigger Condition: Mutual exclusion across instances in the cluster, waiting for other instances to complete loading or modifying the redo head.

### redo remote send

Description: Waiting for redo sending.

Trigger Condition: Waiting occurs when sending redo data to the standby database.

### redo remote sync complete

Description: Waiting for redo synchronization to the standby database.

Trigger Condition: Waiting occurs when a transaction must synchronize redo data to the standby database and is waiting for the flush notification.

### remote dml prepare

Description: The current node waits for other nodes to complete the DML prepare phase.

Trigger Condition: Triggered during multi-node DML execution.

### remote dml start

Description: The current node waits for other nodes to complete DML.

Trigger Condition: Triggered during multi-node queries.

### remote xact wait

Description: A YAC waiting event for row lock conflicts between two instances.

Trigger Condition: Waiting occurs when transaction A in instance 1 acquires a row lock and does not commit, and transaction B in instance 2 attempts to acquire the same lock.

### role create

Description: A YAC waiting event for creating roles.

Trigger Condition: Concurrently creating roles.

### row xact wait

Description: Row lock conflict waiting.

Trigger Condition: Waiting occurs when transaction A acquires a row lock and transaction B tries to acquire the same lock without committing.

<span id="swait" name="swait" class="yaslink"></span>

### scol bulkload busy wait

Description: Waiting occurs during busy bulk loading of stable columnar storage.

Trigger Condition: Waiting occurs when cache for stable columnar storage is full during bulk import.

### scol sync busy wait

Description: Waiting occurs during slow synchronization of stable columnar storage data.

Trigger Condition: Waiting occurs when stable columnar storage data synchronization is slow.

### segment create

Description: A YAC waiting event for segment creation.

Trigger Condition: Waiting occurs when inserting data triggers segment creation; subsequent threads wait for previous threads to complete before continuing.

### set global stats prefs

Description: Waiting for obtaining global mutex lock while setting global statistics options in a YAC.

### setting max protection

Description: Waiting occurs when setting maximize protection mode, waiting for redo synchronization to the synchronized standby.

### shared lock wait

Description: Shared lock conflict waiting.

Trigger Condition: Waiting occurs when transaction A acquires an exclusive lock on a table while transaction B tries to acquire a shared lock on the same table.

### slice exclusive lock wait

Description: Slice exclusive lock conflict waiting.

Trigger Condition: Waiting occurs when transaction A has a shared lock on a slice x of a table, and transaction B attempts to obtain an exclusive lock on the same slice.

### slice shared lock wait

Description: Slice shared lock conflict waiting.

Trigger Condition: Waiting occurs when transaction A has an exclusive lock on a slice x of a table, and transaction B attempts to obtain a shared lock on the same slice.

### space extent

Description: A YAC waiting event, waiting for extent allocation.

Trigger Condition: Triggered during concurrent extent allocation on the same tablespace.

### SQL**** message from client

Description: Database waiting to receive messages from the client.

Trigger Condition: Waiting occurs after database authenticates user information and before the client initiates a business request.

### SQL**** message to client

Description: Database responding to the client.

Trigger Condition: Waiting occurs while the database sends processing results to the client.

### SQL**** more data from client

Description: Database waiting to receive more data from the client.

Trigger Condition: Waiting occurs when the database receives a data package incompletely and must wait for the client to send the rest.

### SQL**** more data to client

Description: Database sending data to the client.

Trigger Condition: Waiting occurs when the database sends a data package that exceeds the size of one package, waiting for part of the package to be sent.

### stats channel receive data

Description: In distributed environments, CN receives statistics from DN via channel.

Trigger Condition: Triggered during statistics collection in a distributed environment.

### swapping in vm block

Description: Loading blocks from SWAP tablespace into memory. For single row excution mode, you can increase the VM_BUFFER_SIZE to cut down the wait time while increase the MEX_POOL_SIZE for batch excution mode.

Trigger Condition: VM needs to access blocks that have been swapped out to disk.

### swapping out vm block

Description: Swapping blocks from memory to SWAP tablespace. For single row excution mode, you can increase the VM_BUFFER_SIZE to cut down the wait time while increase the MEX_POOL_SIZE for batch excution mode.

Trigger Condition: When VM needs memory but there is insufficient space, it will swap out blocks that are not currently in use.

<span id="twait" name="twait" class="yaslink"></span>

### tablespace ctrl

Description: Waiting for control lock conflicts on tablespaces.

Trigger Condition: Waiting occurs when modifying the tablespace control concurrently.

### tablespace manager

Description: A YAC waiting event.

Trigger Condition: Waiting occurs when the parallel execution queue is full for both enqueuing and dequeuing.

<span id="uwait" name="uwait" class="yaslink"></span>

### undo segment extending

Description: Waiting for undo segment extension.

Trigger Condition: Waiting occurs when the undo segment triggers space extension.

### user create

Description: A YAC waiting event for creating users.

Trigger Condition: Concurrently creating users.

<span id="wwait" name="wwait" class="yaslink"></span>

### wait distributed result

Description: CN waiting for response times from various nodes.

Trigger Condition: This time will be recorded for any distributed execution involved.

### wait remote cmd

Description: Waiting for execution commands from remote sessions.

Trigger Condition: Waiting occurs when there's a connection with remote sessions, and there are no ongoing commands executed by this node.

### wait remote result

Description: Waiting for responses from remote nodes.

Trigger Condition: Waiting occurs when partial execution commands are sent to remote nodes.

### window function repartition

Description: Window function repartitioning event.

Trigger Condition: Triggered when the data volume in a single window function partition exceeds a predefined threshold.

### window function sort

Description: Window function sorting event.

Trigger Condition: Triggered when sorting operations are required during window function execution.

<span id="xwait" name="xwait" class="yaslink"></span>

### xa commit

Description: CN waits for all nodes to complete the commit phase of the two-phase commit.

Trigger Condition: Triggered during transaction commits involving multiple DNs in distributed processing.

### xa prepare

Description: CN waits for all nodes to complete the prepare phase of the two-phase commit.

Trigger Condition: Triggered during transaction commits involving multiple DNs in distributed processing.

### xfmr wait for bulkload

Description: Waiting for bulkload import completion for xfmr.

Trigger Condition: Triggered during concurrent alter slice and bulkload insert.

### xslot busy wait

Description: Waiting for block transaction management unit application.

Trigger Condition: Waiting occurs when there is not enough space on the block to apply a transaction management unit.

### ystream server

Description: Waiting for other instances to complete modifications to the YStream server system table.

Trigger Condition: Mutual exclusion with cluster instances while waiting for other instances' modifications.