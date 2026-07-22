In YashanDB, views such as V$SYSSTAT, V$SESSTAT, and V$MYSTAT display the current statistics related to performance metrics of the system. These metrics reflect the operation status of some key internal activities during the system running process, including user-executed activities, activities related to redo logs, cache-related activities, SQL execution-related activities, and more.

This article lists and explains each specific activity corresponding to a statistical item.
### APPLICATION WAIT TIME

Description: Total wait time for wait events of the Application type.

### ARCH DATA CHECK LFN

Description: The maximum LFN covered in the primary database Arch Data.

### ARCH DATA LFN

Description: Cleanable LFN in Arch Data archiving.

### ARCH DATA STATISTICS LFN

Description: Maximum LFN used for node statistics of the total size of archived stable files.

### ARCH DATA STATISTICS SIZE

Description: Total size of node archived stable files.

### ARCH DATA STBY MIN LFN

Description: Minimum Arch Data LFN for the node and its standby database and cascade standby.

### BLOCK CHANGES

Description: Number of block modifications.

### BUFFER BLOCKS INSPECTED

Description: Number of blocks accessed during the eviction process.

### BUFFER BLOCKS READAHEAD

Description: Number of blocks read ahead.

### BUFFER BLOCKS READS

Description: Number of blocks read.

### BUFFER BLOCKS SWAPS

Description: Number of blocks swapped out.

### BUFFER BLOCKS WRITES

Description: Number of blocks written.

### BUFFER CR GETS

Description: Number of CR blocks read.

### BUFFER DIRTY INSPECTED

Description: Number of dirty blocks encountered when evicting to obtain an available buffer.

### BUFFER FREE REQUEST

Description: Number of requests to evict an available buffer.

### BUFFER GETS

Description: Number of current blocks read.

### BUFFER HOT BLOCK MOVES

Description: Number of hot blocks moved during eviction.

### BUFFER IS NOT PINNED

Description: Access to an idle block.

### BUFFER IS PINNED

Description: Access to a block with lock conflicts.

### BUFFER PINNED INSPECTED

Description: Number of blocks currently in use encountered when evicting to obtain an available buffer.

### BUFFER READ CACHE HITS

Description: Number of session local cache hits during block read access.

### BUFFER READ CACHE INVALIDS

Description: Number of times the block local cache state was invalidated.

### BUFFER READ CACHE MISS

Description: Number of session local cache misses during block read access.

### BUFFER READ CACHE TOTAL

Description: Total number of times blocks were added to the session local cache.

### BUFFER READ-AHEAD DISK READ BLOCKS

Description: Total number of pages read from data file during asynchronous pre-read process.

### BUFFER READ-AHEAD DISK READ COUNT

Description: Number of IOs reading from the data file during asynchronous pre-read process.

### BUFFER READ-AHEAD DISK READ TIME

Description: Total IO time consuming during data file reads in asynchronous pre-read process.

### BUFFER READ-AHEAD FAIL COUNT

Description: Number of block load failures during the asynchronous pre-read process.

### BUFFER READ-AHEAD LOAD COUNT

Description: Total number of block loads requested from the queue during the asynchronous pre-read process.

### BUFFER READ-AHEAD LOAD TIME

Description: Total load time for all blocks in the request queue during the asynchronous pre-read process.

### BUFFER READ-AHEAD POP COUNT

Description: Number of times requests were extracted from the block request queue during asynchronous pre-read.

### BUFFER READ-AHEAD POP NEIGHBORS (  1)

Description: Number of times one page was fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS (  2)

Description: Number of times two adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS (  4)

Description: Number of times three to four adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS (  8)

Description: Number of times five to eight adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS ( 16)

Description: Number of times nine to sixteen adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS ( 32)

Description: Number of times seventeen to thirty-two adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS ( 64)

Description: Number of times thirty-three to sixty-four adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP NEIGHBORS (INF)

Description: Number of times more than sixty-five adjacent pages were fetched at a time during the asynchronous pre-read from the request queue.

### BUFFER READ-AHEAD POP TIME

Description: Time taken to extract requests from the block request queue during asynchronous pre-read.

### BUFFER READ-AHEAD QUEUE SIZE

Description: Length of the request queue for asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS (  1)

Description: Number of times one page was read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS (  2)

Description: Number of times two adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS (  4)

Description: Number of times three to four adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS (  8)

Description: Number of times five to eight adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS ( 16)

Description: Number of times nine to sixteen adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS ( 32)

Description: Number of times seventeen to thirty-two adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS ( 64)

Description: Number of times thirty-three to sixty-four adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD READ NEIGHBORS (INF)

Description: Number of times more than sixty-five adjacent pages were read at a time from the data file during asynchronous pre-read.

### BUFFER READ-AHEAD SORT COUNT

Description: Number of times the block request queue was sorted during the asynchronous pre-read process.

### BUFFER READ-AHEAD SORT TIME

Description: Time taken to sort the block request queue during the asynchronous pre-read process.

### BYTES RECEIVED VIA SQL*NET FROM CLIENT

Description: Reserved field.

### BYTES SENT VIA PX

Description: Amount of data sent via PX on the network.

### BYTES SENT VIA SQL*NET TO CLIENT

Description: Reserved field.

### CHECKPOINTS COMPLETED

Description: Number of completed checkpoints.

### CHECKPOINTS STARTED

Description: Number of checkpoints triggered due to insufficient redo or timeout.

### CLEANOUT AND ROLLBACK - CURRENT BLOCKS

Description: Current block has transactions that need to be reverted and has Xslots requiring cleanup.

### CLEANOUT ONLY - CURRENT BLOCKS

Description: Current block is directly available and does not need to construct CR, but has uncleaned Xslots that require cleaning.

### COLUMNAR JOIN FILES

Description: Number of persistent files generated by columnar join operators.

### COLUMNAR MATERIAL FILES

Description: Number of persistent files generated by columnar materialization operators.

### COLUMNAR SORT FILES

Description: Number of persistent files generated by columnar sort operators.

### COMMIT CLEANOUT FAIL: BLOCK MISS

Description: Number of failures in cleanout due to the block not being in the buffer during the commit process.

### COMMIT CLEANOUTS

Description: Number of cleanout block calls during the commit process.

### COMMIT CLEANOUTS DELAYED

Description: Number of blocks where deferred cleanouts exceeded buffer thresholds and were not cleaned.

### COMMIT CLEANOUTS SUCCESSFULLY COMPLETED

Description: Number of blocks successfully cleaned out during the commit process.

### COMMITS

Description: Number of commits initiated by users.

### CONCURRENCY WAIT TIME

Description: Total wait time for wait events of the Concurrency type.

### CONSISTENT CHANGES

Description: Number of transactions that built CR rollbacks.

### CPU TIME

Description: Total CPU time consumed during SQL execution.

### CR BLOCKS CREATED

Description: Number of CR pages constructed.

### DATA BLOCK CONSISTENT READ - UNDO RECORD APPLIED

Description: Undo records applied during the construction of CR.

### DB TIME

Description: Total time of SQL execution.

### DBWR BUFFERS SCANNED

Description: Number of blocks scanned by the database writer due to buffer cleaning.

### DBWR CHECKPOINT BUFFER WRITES

Description: Number of dirty pages flushed by the database writer during a checkpoint.

### DBWR CHECKPOINTS

Description: Number of checkpoints executed by the database writer.

### DBWR FLUSH NEIGHBORS COUNT (   1)

Description: Number of times the database writer flushed one block.

### DBWR FLUSH NEIGHBORS COUNT (   2)

Description: Number of times the database writer flushed two blocks.

### DBWR FLUSH NEIGHBORS COUNT (   4)

Description: Number of times the database writer flushed (2,4] blocks.

### DBWR FLUSH NEIGHBORS COUNT (   8)

Description: Number of times the database writer flushed (4,8] blocks.

### DBWR FLUSH NEIGHBORS COUNT (  16)

Description: Number of times the database writer flushed (8,16] blocks.

### DBWR FLUSH NEIGHBORS COUNT (  32)

Description: Number of times the database writer flushed (16,32] blocks.

### DBWR FLUSH NEIGHBORS COUNT (  64)

Description: Number of times the database writer flushed (32,64] blocks.

### DBWR FLUSH NEIGHBORS COUNT ( 128)

Description: Number of times the database writer flushed (64,128] blocks.

### DBWR FLUSH NEIGHBORS COUNT ( 256)

Description: Number of times the database writer flushed (128,256] blocks.

### DBWR FLUSH NEIGHBORS COUNT ( 512)

Description: Number of times the database writer flushed (256,512] blocks.

### DBWR FLUSH NEIGHBORS COUNT (1024)

Description: Number of times the database writer flushed (512,1024] blocks.

### DBWR FLUSH NEIGHBORS COUNT (INF)

Description: Number of times the database writer flushed more than 1024 blocks.

### DBWR FREE BUFFERS FOUND

Description: Number of blocks flushed to disk by the database writer due to buffer cleaning.

### DBWR LRU SCANS

Description: Number of times the database writer scanned the LRU due to buffer cleaning, including requests and scheduled tasks.

### DBWR MAKE FREE REQUEST

Description: Number of buffer cleaning requests executed by the database writer.

### DBWR PC CLEAN REQUESTS

Description: Number of times the database writer was awakened by past copy writes, used for statistics of dbwr invocation in the YAC environment when writing to past copy owner.

### DBWR PC OWNER WRITES

Description: Number of past copy owners flushed by the database writer, used to count the number of times the past copy owner page was written in the YAC environment.

### DBWR REVISITED MARKED BLOCKS

Description: Number of blocks encountered by the database writer that were already in the process of being flushed.

### DBWR ROLLBACK BLOCK WRITES

Description: Number of undo blocks flushed by the database writer.

### DBWR ROLLBACK SEGMENT WRITES

Description: Number of undo segments flushed by the database writer, used to count the number of times hot pages were flushed.

### DEADLOCK DETECTION

Description: Number of deadlock detection executions.

### DEADLOCK DETECTION TIME

Description: Total wait time for deadlock detection execution.

### DELETE COUNT

Description: Total number of DELETE executions.

### DICT LOAD COUNT

Description: Number of times the dictionary cache was loaded from disk.

### DISK READ TIME

Description: Total time spent reading blocks from disk.

### DISK READS

Description: Total number of blocks read from disk.

### DISK WRITE TIME

Description: Total time spent flushing blocks to disk.

### DISK WRITES

Description: Total number of block flushes to disk.

### DISKCACHE EVICT CNT

Description: Total number of evictions from disk buffer.

### DISKCACHE HIT CNT

Description: Total number of hits in disk buffer.

### DISKCACHE MISS CNT

Description: Total number of misses in disk buffer.

### DISKCACHE READ BYTES

Description: Total bytes read from disk buffer.

### DISKCACHE READ CNT

Description: Total number of read requests from disk buffer.

### DISKCACHE READ TIME

Description: Total time spent reading from disk buffer, in milliseconds.

### DISKCACHE WRITE BYTES

Description: Total bytes written to disk buffer.

### DISKCACHE WRITE CNT

Description: Total number of write requests to disk buffer.

### DISKCACHE WRITE TIME

Description: Total time spent writing to disk buffer, in milliseconds.

### EXECUTE COUNT

Description: Total command execution count, including error DDLs, incremented regardless of success or failure.

### EXT ALLOCATE COUNT

Description: Number of extent requests for objects.

### EXT EXTEND COUNT

Description: Number of extent requests that triggered data file extensions.

### FLASHBACK LOG WRITE BYTES

Description: Total bytes written to the flashback log file for the entire database.

### FLASHBACK LOG WRITES

Description: Total number of writes to the flashback log file for the entire database.

### GC BLOCK INVALIDATES

Description: Number of invalid block requests in the cluster field.

### GC BUFFER BUSY WAITS

Description: Number of buffer wait requests in the cluster field.

### GC CLEAN BLOCKS

Description: Number of blocks cleaned in the cluster field.

### GC CLOSE BLOCK RETRIES

Description: Number of retry attempts for closing block messages after page acquisition in the cluster field.

### GC CR BLOCK RECEIVE TIME

Description: Time taken to receive CR blocks from other instances in the cluster field.

### GC CR BLOCK RECEIVED

Description: Number of CR blocks received from other instances in the cluster field.

### GC CR BLOCK RETRIES

Description: Number of retry attempts for requesting CR blocks from other instances in the cluster field.

### GC CR BLOCK SERVED

Description: Number of CR blocks sent to other instances in the cluster field.

### GC CURRENT BLOCK RECEIVE TIME

Description: Time taken to receive current blocks from other instances in the cluster field.

### GC CURRENT BLOCK RECEIVED

Description: Number of current blocks received from other instances in the cluster field.

### GC CURRENT BLOCK RETRIES

Description: Number of retry attempts for requesting current blocks from this instance in the cluster field.

### GC CURRENT BLOCK SERVED

Description: Number of current blocks sent to other instances in the cluster field.

### GC DEGRADE BLOCK LOCKS

Description: Cluster field, number of remote request degraded free block locks.

### GC FLUSH BLOCK RETRIES

Description: Number of retries triggered for flushing when sending or invalidating pages in the cluster field.

### GC FLUSH BLOCKS

Description: Number of flushes triggered when sending or invalidating pages in the cluster field.

### GC INVALIDATE BLOCK LOCKS

Description: Cluster field, number of remote request invalidated free block locks.

### GC LOCAL CR GRANT TIME

Description: Time taken for this instance to authorize loading pages for CR requests in the cluster field.

### GC LOCAL CR GRANTS

Description: Number of authorization requests to load pages for local CR in the cluster field.

### GC LOCAL GRANT LOCKS

Description: Number of grants for local locks by this instance in the cluster field.

### GC LOCAL GRANT TIME

Description: Time taken for local grants by this instance in the cluster field.

### GC LOCAL GRANTS

Description: Number of grants for loading pages by this instance in the cluster field.

### GC LOCAL RELEASE LOCKS

Description: Number of local lock releases by this instance in the cluster field.

### GC LOCAL UPGRADE TIME

Description: Time taken to upgrade page locks by this instance in the cluster field.

### GC LOCAL UPGRADES

Description: Number of local page lock upgrades by this instance in the cluster field.

### GC LWLOCK S

Description: Number of requests for GLS SHARED locks.

### GC LWLOCK S TIME

Description: Total wait time for requests for GLS SHARED locks.

### GC LWLOCK X

Description: Number of requests for GLS EXCLUSIVE locks.

### GC LWLOCK X TIME

Description: Total wait time for requests for GLS EXCLUSIVE locks.

### GC MUTEX

Description: Number of requests for GLS MUTEX locks.

### GC MUTEX TIME

Description: Total wait time for requests for GLS MUTEX locks.

### GC NOTIFY FLUSH

Description: Number of notifications for rdWrite flush redo in the cluster field.

### GC RA BLOCK GRANTS

Description: Number of page pre-read requests in the cluster field.

### GC RA BLOCK LOCK GRANTS

Description: Cluster field, number of times data block locks were pre-applied.

### GC RA BLOCK LOCK SKIPPED

Description: Cluster field, number of times data block lock pre-application was skipped.

### GC RA BLOCK RETRIES

Description: Number of retry attempts for block pre-read requests by this instance in the cluster field.

### GC RECYCLE BLOCK RETRIES

Description: Number of retry attempts for requesting recyclable page resources in the cluster field.

### GC RECYCLE LOCK

Description: Number of times recycled GLS lock memory was evicted.

### GC RECYCLE LOCK TIME

Description: Total wait time for recycling reclaimed GLS lock memory in the cluster field.

### GC REMOTE CR GRANT TIME

Description: Time taken for remote CR requests to authorize page loading in the cluster field.

### GC REMOTE CR GRANTS

Description: Number of remote requests for page loading authorization in the cluster field.

### GC REMOTE GRANT LOCKS

Description: Number of remote grants for locks in the cluster field.

### GC REMOTE GRANT TIME

Description: Time taken for remote grants to load pages in the cluster field.

### GC REMOTE GRANTS

Description: Number of remote grants for loading pages in the cluster field.

### GC REMOTE RELEASE LOCKS

Description: Number of remote lock releases in the cluster fields.

### GC REMOTE UPGRADE TIME

Description: Time taken to upgrade page locks remotely in the cluster field.

### GC REMOTE UPGRADES

Description: Number of remote page lock upgrades in the cluster field.

### GC SKIP CLEAN BLOCKS

Description: Number of pages skipped from cleaning by the local instance holding pastcopy in the cluster field.

### GC TASK EXTEND TIMES

Description: Number of times background threads increased in the cluster field.

### GC TASK SHRINK TIMES

Description: Number of times background threads shrank in the cluster field.

### GC UPGRADE RETRIES

Description: Number of retry attempts to upgrade page locks by this instance in the cluster field.

### GCS ALLOC LOCKS

Description: Cluster field, number of times GCS locks were allocated.

### GCS DETACH LOCKS

Description: Cluster field, number of times GCS locks were detached.

### GCS FREE LOCK TIME

Description: Cluster field, time spent actively eliminating GCS locks.

### GCS FREE LOCKS

Description: Cluster field, number of times GCS locks were actively eliminated.

### GCS GET LOCKS

Description: Cluster field, number of times GCS locks were acquired.

### GCS RELEASE LOCKS

Description: Cluster field, number of times GCS locks were released.

### GCS WAKEUP RECYCLE

Description: Cluster field, number of times background GCS lock recycling was awakened.

### GET REMOTE XACT INFO

Description: Number of attempts to obtain other instances' transaction information in the cluster field.

### GET REMOTE XACT INFO TIME

Description: Time taken to obtain other instances' transaction information in the cluster field.

### GET REMOTE XACT STATUS

Description: Number of attempts to get the status of other instances' transactions in the cluster field.

### GET REMOTE XACT STATUS TIME

Description: Time taken to obtain the status of other instances' transactions in the cluster field.

### GLOBAL CR BLOCKS CREATED

Description: Creation of CR in global buffer.

### HEAP FETCH BY ROWID

Description: Number of rows scanned via Rowid.

### HEAP FETCH MIGR ROW

Description: Number of migrated or linked rows encountered.

### HEAP SCAN BLOCKS FETCH

Description: Number of blocks scanned during full table scan.

### HEAP SCAN ROWS FETCH

Description: Total number of rows scanned during full table scan.

### IDLE WAIT COUNT

Description: Total number of waits for events of the Idle type.

### IDLE WAIT TIME

Description: Total wait time for events of the Idle type.

### INDEX 90-10 SPLIT

Description: Number of 90-10 splits in index leaf nodes.

### INDEX BRANCH SPLIT

Description: Number of splits in index branch nodes.

### INDEX CACHE APPLY SPINS

Description: Number of lock attempts when generating index buffer pages during insert, delete, or update operations.

### INDEX LEAF SPLIT

Description: Number of splits in index leaf nodes.

### INDEX ROOT SPLIT

Description: Number of splits in index root nodes.

### INDEX SCAN SPINS

Description: Number of lock attempts when generating index buffer pages during query operations.

### INSERT COUNT

Description: Total number of INSERT executions.

### LOGONS CURRENT

Description: Total number of currently logged-in sessions.

### LOGONS TOTAL

Description: Total number of sessions logged in cumulatively.

### MCOL BATCH FETCH

Description: Number of batch fetches during MCOL scanning.

### MCOL CACHE REFRESH COUNT

Description: Number of hot page buffer refreshes during MCOL scanning.

### MCOL DICT MISS COUNTS

Description: Number of metadata buffer misses during MCOL scanning.

### MCOL HOLD BLOCKS

Description: Number of blocks that are kept without copies during MCOL scanning.

### MCOL MULTI FETCH

Description: Number of multi-row scanning interface calls during MCOL.

### MCOL ORDER KEY SCAN SLICES

Description: Number of slices scanned using Order Key in MCOL.

### MCOL ROW FETCH

Description: Number of single-row scanning interface calls during MCOL.

### MCOL ROWID FETCH

Description: Number of Rowid-based scanning interface calls during MCOL.

### MCOL SCAN BATCHES

Description: Number of batches accessed during MCOL scanning.

### MCOL SCAN BLOCKS

Description: Number of blocks accessed during MCOL scanning.

### MCOL SCAN SIZE

Description: Size of data scanned during MCOL.

### MCOL SCAN SLICES

Description: Total number of slices scanned during MCOL.

### MCOL SCAN TIME

Description: Total time spent scanning in MCOL.

### METAAGENT CALL META NUM

Description: Number of interactions with metadata by META AGENT.

### METAAGENT CALL META TIME

Description: Time spent on interactions with metadata by META AGENT.

### NO WORK - CR BLOCKS

Description: Number of times CR blocks were directly available without needing to be rebuilt.

### NO WORK - CURRENT BLOCKS

Description: Number of times current blocks were directly available without needing to build CR.

### NON IDLE WAIT TIME

Description: Reserved field.

### NON IDLE WAIT TIME COUNT

Description: Reserved field.

### OPENED CURSORS CURRENT

Description: Total number of currently opened cursors.

### PARALLEL OPERATIONS DOWNGRADED 1 TO 25 PCT

Description: Number of times parallel execution was downgraded to a specific ratio due to resource constraints.

### PARALLEL OPERATIONS DOWNGRADED 25 TO 50 PCT

Description: Number of times parallel execution was downgraded to a specific ratio due to resource constraints.

### PARALLEL OPERATIONS DOWNGRADED 50 TO 75 PCT

Description: Number of times parallel execution was downgraded to a specific ratio due to resource constraints.

### PARALLEL OPERATIONS DOWNGRADED 75 TO 99 PCT

Description: Number of times parallel execution was downgraded to a specific ratio due to resource constraints.

### PARALLEL OPERATIONS DOWNGRADED TO SERIAL

Description: Number of times parallel execution was shut down within operators due to insufficient resources.

### PARALLEL OPERATIONS NOT DOWNGRADED

Description: Number of times parallel execution was performed according to initial parallelism.

### PARSE COUNT (HARD)

Description: Total number of hard parses for SQL.

### PN METACACHE EVICT NUM

Description: Number of evictions from the PN metadata buffer.

### PN METACACHE HITS NUM

Description: Number of hits in the PN metadata buffer.

### PN METACACHE INVALID LOAD NUM

Description: Number of invalid loads in the PN metadata buffer.

### PN METACACHE INVALID LOAD SIZE

Description: Size of invalid loads in the PN metadata buffer.

### PN METACACHE INVALID LOAD TIME

Description: Time taken for invalid loads in the PN metadata buffer.

### PN METACACHE LOAD NUM

Description: Number of loads in the PN metadata buffer.

### PN METACACHE LOAD SIZE

Description: Size of loads in the PN metadata buffer.

### PN METACACHE LOAD TIME

Description: Time taken for loads in the PN metadata buffer.

### PN METACACHE MISS NUM

Description: Number of misses in the PN metadata buffer.

### PN METACACHE TEMP LOAD NUM

Description: Number of temporary loads in the PN metadata buffer.

### PN METACACHE TEMP LOAD SIZE

Description: Size of temporary loads in the PN metadata buffer.

### PN METACACHE TEMP LOAD TIME

Description: Time taken for temporary loads in the PN metadata buffer.

### QUERY COUNT

Description: Total number of queries executed.

### RECURSIVE CALLS

Description: Number of recursive calls. Each time SQL is executed, the number of additional SQL calls generated by it.

### REDO BLOCKS WRITTEN

Description: Total number of redo blocks written to disk.

### REDO ENTRIES BY OTHER

Description: Total number of times a session copied private redo from other sessions to the global redo buffer.

### REDO ENTRIES BY SELF

Description: Total number of times a session copied its private redo to the global redo buffer.

### REDO LOG SPACE REQUESTS

Description: Number of redo writes triggered due to insufficient redo buffer space.

### REDO SIZE

Description: Total size of redo (in bytes).

### REDO SWITCH WAIT TIME

Description: Wait time for redo switch.

### REDO SYNCH TIME

Description: Total time for redo flush triggered by transaction commits.

### REDO SYNCH WRITES

Description: Total number of redo flushes triggered by transaction commits.

### REDO WASTAGE

Description: Size of wasted space during redo flush (in bytes).

### REDO WRITE SIZE COUNT (  512B)

Description: Number of redo blocks between (0, 512].

### REDO WRITE SIZE COUNT (   1KB)

Description: Number of redo blocks between (512, 1KB].

### REDO WRITE SIZE COUNT (   2KB)

Description: Number of redo blocks between (1KB, 2KB].

### REDO WRITE SIZE COUNT (   4KB)

Description: Number of redo blocks between (2KB, 4KB].

### REDO WRITE SIZE COUNT (   8KB)

Description: Number of redo blocks between (4KB, 8KB].

### REDO WRITE SIZE COUNT (  16KB)

Description: Number of redo blocks between (8KB, 16KB].

### REDO WRITE SIZE COUNT (  32KB)

Description: Number of redo blocks between (16KB, 32KB].

### REDO WRITE SIZE COUNT (  64KB)

Description: Number of redo blocks between (32KB, 64KB].

### REDO WRITE SIZE COUNT ( 128KB)

Description: Number of redo blocks between (64KB, 128KB].

### REDO WRITE SIZE COUNT ( 256KB)

Description: Number of redo blocks between (128KB, 256KB].

### REDO WRITE SIZE COUNT ( 512KB)

Description: Number of redo blocks between (256KB, 512KB].

### REDO WRITE SIZE COUNT (1024KB)

Description: Number of redo blocks between (512KB, 1MB].

### REDO WRITE SIZE COUNT (INF)

Description: Number of redo blocks of size 1MB or larger.

### REDO WRITE TIME

Description: Total time for redo flush.

### REDO WRITES

Description: Total number of redo flushes.

### REMOTE XACT WAIT TIME

Description: Total wait time for remote transactions in the cluster field.

### REMOTE XACT WAITS

Description: Number of remote transaction waits in the cluster field.

### REMOTE XSLOT CHECK

Description: Number of remote transaction management unit checks in the cluster field.

### REMOTE XSLOT CHECK TIME

Description: Waiting time for remote transaction management unit checks in the cluster field.

### RESTART ATOMICS - SSM ADD BLOCK

Description: Number of times atomic operations were restarted due to rollback while formatting data blocks.

### ROLLBACK CHANGES - UNDO RECORD APPLIED

Description: Number of records rolled back using undo records during user-initiated rollbacks.

### ROLLBACK ONLY - CURRENT BLOCKS

Description: Current block has transactions that cannot be reconciled that need to construct CR but no uncleaned Xslots.

### ROLLBACKS

Description: Total number of user-initiated explicit rollbacks.

### ROLLBACKS - CR BLOCKS

Description: Number of CR blocks constructed.

### SCOL BULKLOAD COMMIT TIME

Description: Time spent in the bulk load commit phase, in microseconds.

### SCOL BULKLOAD FLUSH CNT

Description: Number of flushes during bulk load buffer writes.

### SCOL BULKLOAD SWAP CNT

Description: Number of swaps caused by bulk load imports.

### SCOL BULKLOAD SWAP SIZE

Description: Number of bytes swapped due to insufficient buffer during bulk load import.

### SCOL BULKLOAD SWAP TIME

Description: Time taken for swaps due to insufficient buffer during bulk load import, in microseconds.

### SCOL BULKLOAD TIME

Description: Time taken for storage side of bulk load, in microseconds.

### SCOL COMPRESS BYTES

Description: Number of bytes compressed in SCOL.

### SCOL COMPRESS TIME

Description: Time taken to compress in SCOL.

### SCOL DECOMPRESS BYTES

Description: Number of bytes decompressed in SCOL.

### SCOL DECOMPRESS TIME

Description: Time taken to decompress in SCOL.

### SCOL HIT NUM

Description: Total number of hits in SCOL buffer.

### SCOL MATCH ROW GROUP

Description: Number of row groups to scan after filtering in SCOL.

### SCOL MATCH ROWS

Description: Number of returned rows after filtering in SCOL.

### SCOL MATCH SLICE

Description: Number of slices to scan after filtering in SCOL.

### SCOL MISS NUM

Description: Total number of misses in SCOL buffer.

### SCOL READ BYTES

Description: Number of bytes read during static file read in SCOL.

### SCOL READ CNT

Description: Number of disk requests for static file read in SCOL.

### SCOL READ TIME

Description: Time spent reading static files in SCOL.

### SCOL SCAN ROW GROUP

Description: Number of row groups to scan after filtering slices in SCOL.

### SCOL SCAN ROWS

Description: Number of rows to scan after filtering row groups in SCOL.

### SCOL SCAN SLICE

Description: Number of slices that need to be scanned in SCOL.

### SCOL SCAN TIME

Description: Total time spent scanning in SCOL.

### SCOL SWAP IN ALLOC BYTES

Description: Number of bytes requested during the import/background conversion process due to swap in.

### SCOL SWAP IN CNT

Description: Number of swap ins due to insufficient COLUMNAR_VM_BUFFER during import/background conversion.

### SCOL SWAP IN READ BYTES

Description: Number of bytes read from disk during the import/background conversion process due to insufficient COLUMNAR_VM_BUFFER.

### SCOL SWAP IN TIME

Description: Time spent swapping in during the import/background conversion process due to insufficient COLUMNAR_VM_BUFFER.

### SCOL SWAP OUT CNT

Description: Number of swap outs due to insufficient COLUMNAR_VM_BUFFER during import/background conversion.

### SCOL SWAP OUT FREE BYTES

Description: Number of bytes released during swap out during import/background conversion.

### SCOL SWAP OUT TIME

Description: Time taken for swap out during import/background conversion due to insufficient COLUMNAR_VM_BUFFER.

### SCOL SWAP OUT WRITE BYTES

Description: Number of bytes written to disk during the import/background conversion process due to insufficient COLUMNAR_VM_BUFFER.

### SCOL SYNC CNT

Description: Total number of sync sends in SCOL.

### SCOL SYNC FILE SYSTEM TIME

Description: Time spent on file system buffer sync in SCOL.

### SCOL SYNC READ BYTES

Description: Total number of bytes read from disk during sync in SCOL.

### SCOL SYNC READ CNT

Description: Total number of reads from disk during sync in SCOL.

### SCOL SYNC READ TIME

Description: Total time spent reading from disk during sync, in microseconds.

### SCOL SYNC TIME

Description: Total time spent sending during sync, in microseconds.

### SCOL SYNC WRITE BYTES

Description: Total bytes written to disk during sync in SCOL.

### SCOL SYNC WRITE CNT

Description: Total number of writes to disk during sync in SCOL.

### SCOL SYNC WRITE TIME

Description: Total time spent writing to disk during sync, in microseconds.

### SCOL TRANSFORM COUNT

Description: Number of slices generated from SCOL transformation.

### SCOL TRANSFORM DATA COST

Description: Time spent generating static files in SCOL transformations.

### SCOL TRANSFORM FULL COUNT

Description: Number of slices generated from full buffer in SCOL transformations.

### SCOL TRANSFORM FULL SORT COUNT

Description: Number of times sorting during SCOL transformations based on default granularity (512K rows).

### SCOL TRANSFORM META COST

Description: Time spent in generating metadata in SCOL transformations.

### SCOL TRANSFORM SORT COUNT

Description: Number of sorts during SCOL transformations.

### SCOL TRANSFORM SORT ROWS

Description: Total number of rows sorted during SCOL transformations.

### SCOL TRANSFORM SORT SWAP BYTES

Description: Number of bytes swapped out during sorting in SCOL transformations.

### SCOL WRITE BYTES

Description: Total number of bytes written during static file generation in SCOL.

### SCOL WRITE CNT

Description: Total number of writes requested to disk during static file generation in SCOL.

### SCOL WRITE TIME

Description: Time spent writing static files in SCOL.

### SO ACCESS CACHE ERROR COUNT

Description: The number of error occurrences in the PL local variable cache optimization feature.

### SO ACCESS CACHE HIT COUNT

Description: The number of cache hits in the PL local variable cache optimization feature.

### SO ACCESS CACHE MISS COUNT

Description: The number of cache misses in the PL local variable cache optimization feature.

### SORTS (DISK)

Description: Reserved field.

### SORTS (MEMORY)

Description: Reserved field.

### SQL REUSE COUNT

Description: Total number of soft parses for SQL.

### SQL*NET ROUNDTRIPS TO/FROM CLIENT

Description: Reserved field.

### SSM CHECK BLOCKS - DATA BLOCKS

Description: Number of data blocks checked during the search process.

### SSM CHECK BLOCKS - INSTANCE CHANGED

Description: Number of times a block was found to no longer belong to this instance upon attaching L1 block.

### SSM CHECK BLOCKS - L1 BLOCKS

Description: Number of L1 blocks checked during the search process.

### SSM CHECK BLOCKS - L2 BLOCKS

Description: Number of L2 blocks checked during the search process.

### SSM CHECK BLOCKS - L3 BLOCKS

Description: Number of L3 blocks checked during the search process.

### SSM MARK DATA BLOCK FULL

Description: Number of blocks marked as full during the search process.

### SSM RANDOM SEARCH

Description: Number of times random searches were conducted via the SSM search entry.

### SSM RANDOM SEARCH BY L1

Description: Number of times random searches were conducted via L1.

### SSM REJECT DATA BLOCKS

Description: Number of blocks that did not meet the insertion criteria during the search process.

### SSM SEGMENT EXTEND

Description: Number of times segments were extended.

### SSM SEQUENCE SEARCH

Description: Number of times sequential searches were conducted.

### SSM STEAL L1

Description: Number of times L1 blocks were seized from other instances.

### TABLE LOCK CACHE HITS

Description: Number of local cache hits when acquiring table locks.

### TABLE LOCK CACHE INVALIDS

Description: Number of times the local cache state of table locks was invalidated.

### TABLE LOCK CACHE MISS

Description: Number of times the local cache missed when acquiring table locks.

### TABLE LOCK CACHE TOTAL

Description: Total number of table locks added to the local cache.

### TABLE WAIT TIME

Description: Duration of table lock waits.

### UPDATE COUNT

Description: Total number of UPDATE command executions.

### USER CALLS

Description: Number of times users ran SQL.

### USER IO WAIT TIME

Description: Total wait time for events of the User I/O type.

### VM ALLOC

Description: Number of VM allocations.

### VM CLOSE

Description: Number of VM closures.

### VM EXTEND

Description: Number of times VM extended pages allocated from swap tables.

### VM FREE

Description: Number of VM releases.

### VM IO WAIT

Description: Number of waits incurred when other sessions were swapping out while opening VM.

### VM OPEN

Description: Number of VM openings.

### VM SWAP IN

Description: Number of VM swaps in.

### VM SWAP OUT

Description: Number of VM swaps out.

### WAIT COMMIT TIME

Description: Number of waits caused by needing to read transaction information that is being proposed.

### XACT CONSISTENT READ - UNDO RECORD APPLIED

Description: Number of times undo records are used to read historical versions of transactions during execution.

### XACT WAIT TIME

Description: Duration of transaction wait.

### XACT WAITS

Description: Number of transaction waits incurred.

### XSLOT WAITS

Description: Number of XSLOT waits incurred.