YashanDB database consists of multiple memory areas, each containing multiple subcomponents.

- Shared Global Area (SGA)

    SGA is a set of memory structures that can be shared by all background threads and sessions, such as data buffer, redo buffer, share pool, etc.

- Session Private Area (SPA)

    SPA is a memory area exclusively allocated and managed by the session, such as session stack memory.

## SGA

SGA is generally shared by multiple sessions or threads, and mainly includes the share pool, data cache, redo buffer, etc.

### Share Pool 

#### Function Overview

The Share Pool is a core area in the database used to cache shared data and metadata. By caching frequently accessed data, it significantly reduces disk I/O and parsing overhead, thereby improving SQL execution efficiency. The cached content primarily includes:

- SQL parse tree

- Execution plan

- Dictionary cache

- Shared cursors

- Session-shared memory structures (e.g., locks, transaction information, etc.)

The memory shared pool consists of the following memory regions:

| Memory Region   | Function   |
| ---- | ---- |
| SQL Pool | A memory cache used during SQL execution. Functionally, it is divided into: <br/>* SQL main pool: Stores SQL parse trees and execution plans. When the SQL engine executes a statement, it first checks the SQL Pool; if an identical statement exists, it uses the already compiled execution plan to avoid hard parsing and save overhead. <br/>* PL pool: After creation, objects like stored procedures, packages, and triggers are loaded into the PL pool to improve the efficiency of subsequent executions.  |
| Dictionary Cache | Used to cache the data dictionary, which includes metadata about tables, indexes, columns, users, privileges, and other database objects. During SQL parsing and execution, the SQL engine accesses the data dictionary to validate object information. As metadata that is accessed extremely frequently, caching significantly improves access efficiency.   |
| Lock Pool | Used to cache global lock resources, supporting lock management and resource coordination during concurrent access by multiple sessions.  |
| User Lock Pool | Used to cache global user lock resources, supporting lock management and resource coordination during concurrent access by multiple sessions.  |
| Cursor Pool | Used to cache cursor resources generated during SQL execution, enabling fast reuse when the same SQL is executed repeatedly.  |
| Recovery Buddy Pool | Used to cache temporary data during recovery, helping the instance quickly access and process redo log data upon crash recovery, accelerating the recovery process. |
| GLS Pool | Exists only in YAC or distributed clusters, used to locally cache metadata of global lock resources, improving lock management efficiency. |
| Block Resource Pool | Exists only in YAC or distributed clusters, used to cache metadata of global resources for data blocks, supporting cross-node data consistency management. |
| Non-Block Resource Pool | Exists only in YAC or distributed clusters, used to cache metadata related to global locks. |
| GCS Lock Pool | Exists only in YAC or distributed clusters, used to cache lock level information for data blocks within the current instance. |
| GCS PC Pool | Exists only in YAC or distributed clusters, used to cache metadata of historical snapshots of the global cache. |
| GRC Request Pool | Exists only in YAC or distributed clusters, used to cache queue management information for global cache and locks. |
| GRC Object Pool | Exists only in YAC or distributed clusters, used to cache metadata of global object affinity rules. |
| Stream Pool | Used to cache metadata, Logical Change Record (LCR) cache, and send/receive queues during YStream logical replication parsing. |
| DSTB Pool | Exists only in ISC distributed clusters, used to cache management information for distributed cluster management and metadata management modules. |
| Free Pool | Redundant memory of the shared memory pool, which can be dynamically allocated to other pools to accommodate load fluctuations or resource demand changes, improving memory resource utilization. |

#### Configuration and Management

- Capacity Configuration: The total capacity of the shared pool is configured via the SHARE_POOL_SIZE parameter.

- Performance Impact: A too-small capacity may cause frequent parsing; a too-large capacity may lead to unnecessary memory waste.

- Tuning Guidance: For systems with high concurrency and high SQL reuse rates, it is recommended to appropriately increase the pool size.

### Data Buffer

#### Function Overview

The Data Buffer is used to cache copies of data blocks read from disk — currently or recently accessed — and is divided into two types: row data buffer and column data buffer. Row data buffer is used to cache copies of data blocks for row-store tables, as well as metadata management and hot data blocks for LSC tables. Column data buffer is used to cache copies of data blocks for cold data in LSC tables.

When a user or session accesses a specific data block:

- If the block is already in cache (also called "cache hit"), it is read directly from memory, without accessing disk.

- If the block is not in cache (called "cache miss"), the block is read from disk and loaded into cache.

LRU algorithm is used to manage the cache. When memory pressure occurs — for example, when memory needs to be reclaimed for reuse — the system evicts the least recently used data blocks based on usage frequency.

#### Configuration and Management

- Capacity Configuration: 

    - The capacity of the row data cache is configured via the DATA_BUFFER_SIZE parameter.

    - The capacity of the column data cache is determined jointly by the COLUMNAR_BUFFER_SIZE parameter and the COLUMNAR_DATA_BUFFER_PERCENT parameter, with the calculation formula: COLUMNAR_BUFFER_SIZE * (100 - COLUMNAR_DATA_BUFFER_PERCENT) / 100.

- Performance Impact: The capacity of this cache region directly affects database performance. A too-small capacity leads to frequent disk I/O, while a too-large capacity may cause memory waste.

- Tuning Guidance: Set the cache region size appropriately based on data volume and access patterns.

### Redo Buffer

#### Function Overview

The Redo Buffer is used to cache redo logs generated by database transactions. Before a transaction is committed, the logs are first written into this cache, and then the redo disk-flushing thread (LOGW) periodically writes the cached logs to disk, or flushes them in batches when the redo log count reaches a threshold, thereby reducing disk I/O frequency and improving write performance.

#### Configuration and Management

- Capacity Configuration: The capacity of the redo buffer is configured via the REDO_BUFFER_SIZE parameter.

- Performance Impact: The size of the redo buffer affects transaction write latency and recovery performance.

- Tuning Guidance: In high-write-load scenarios, appropriately increasing the cache size can reduce log write latency.

###  Temporary Buffer

#### Function Overview

The Temporary Buffer is the pure in-memory portion of virtual memory, primarily used for sorting during index builds, TEMP_LOB caching, and materialized buffer areas for single-row execution operators.

When memory in this cache becomes insufficient, the system will swap out part of the data to the SWAP tablespace (i.e., use disk space to extend memory), thereby providing capacity space exceeding the size of the temporary buffer.

#### Configuration and Management

- Capacity Configuration: The capacity of the temporary cache is configured via the VM_BUFFER_SIZE parameter.

- Performance Impact: Frequent swapping in and out will degrade performance.

- Tuning Guidance: In scenarios such as index building and large-scale data processing, appropriately increasing the cache size can reduce swapping frequency and improve performance. Additionally, it can be tuned in conjunction with the SWAP tablespace — for example, by increasing the number of swap files or enabling automatic file size expansion.

###  DBWR Buffer

#### Function Overview

The DBWR (Database Writer) Buffer is the memory region used by the database writer thread (named `DBWR`) to temporarily store dirty data blocks (i.e., data blocks that have been modified but not yet written to disk). The DBWR thread periodically writes dirty data blocks from this cache to disk, ensuring data persistence.

#### Configuration and Management

- Capacity Configuration: The memory capacity of the DBWR Buffer is determined jointly by the DBWR_BUFFER_SIZE parameter and the DBWR_COUNT parameter, with the calculation formula: DBWR_BUFFER_SIZE * DBWR_COUNT.

- Performance Impact: Increasing this pool reduces disk I/O, but consumes more memory.

- Tuning Guidance: In high-concurrency write scenarios, appropriately increasing the pool size can improve write performance.

###  Hot Cache

#### Function Overview

The Hot Cache is used to cache database pages that are frequently read. By preloading hot data, it reduces disk I/O and lowers response latency, thereby improving query performance.

The system automatically identifies hot pages and preloads potentially accessed data pages into the cache before query execution.

#### Configuration and Management

Memory for the hot cache is automatically allocated and reclaimed by the system from the total capacity of SGA.

###  Large Pool

#### Function Overview

The Large Pool is used to allocate and cache large objects exceeding the size of a single page, such as very large SQL text, JSON data, XML data, LOB data, etc., to avoid frequent allocation and deallocation of small memory blocks in the shared pool.

#### Configuration and Management

- Capacity Configuration: The capacity of the large object pool is configured via the LARGE_POOL_SIZE parameter.

- Performance Impact: Suitable for scenarios involving frequent large object operations, such as bulk imports, JSON processing, etc.

- Tuning Guidance: In scenarios with frequent large object operations, it is recommended to configure an appropriate size to avoid memory shortages or wastage.

###  Job Pool

#### Function Overview

The Job Pool is used to support the creation, scheduling, and execution of JOBs, including:

- Allocation of execution context for JOBs

- Memory management for the job scheduler

- Allocation and deallocation of execution heaps for tasks

Each JOB uses an independent memory context, which can only be reused by other JOBs after its memory is released. When memory in this cache region becomes insufficient, the system evicts the execution contexts of jobs that have not been executed for a long time.

#### Configuration and Management

Memory for the job pool is automatically allocated and reclaimed by the system from the total capacity of SGA.

###  Audit Queue Buffer

#### Function Overview

The Audit Queue Buffer is used to cache asynchronous audit logs. When asynchronous audit log writing is enabled, the database first caches audit logs in this area, and then writes them in batches to system tables or external files when conditions are met (e.g., the queue is full or a scheduled flush occurs).

#### Configuration and Management

- Capacity Configuration: The capacity of the audit global cache is configured via the AUDIT_QUEUE_SIZE parameter.

- Performance Impact: Affects audit log write latency and disk I/O.

- Tuning Guidance: In high-audit-load scenarios, it is recommended to appropriately increase the pool size to avoid frequent flushes.

<span id="GlobalApp" name="GlobalApp"></span>

###  Global Application Pool

#### Function Overview

The Global Application Pool is a global memory pool used by the SQL engine during query execution, primarily for generating execution heap objects.

When a session's private application pool is insufficient, memory can be dynamically allocated from this pool during SQL execution and automatically released after execution completes.

#### Configuration and Management

- Capacity Configuration: The capacity of the Global Application Pool is configured via the WORK_AREA_POOL_SIZE parameter.

- Performance Impact: Insufficient Global Application Pool capacity may cause sessions to frequently fail memory allocation requests, affecting execution success rate.

- Tuning Guidance: Configure appropriately based on the number of concurrent sessions and execution workload.

## SPA

The SPA is a dedicated memory area allocated when a session is created. Unlike the shared memory region, this area is exclusively owned and managed by the session, and is released upon session termination. This region primarily satisfies various memory space requirements during SQL execution. It mainly includes:

- Columnar VM Buffer

- Stack Memory

- Heap Memory

###  Columnar VM Buffer

#### Function Overview

The Columnar VM Buffer is used to materialize and cache objects during the SQL engine's execution phase, and is primarily used when SQL operators compute data from column-store tables.

When memory in this cache becomes insufficient, the system will swap out part of the data to the SWAP tablespace (i.e., use disk space to extend memory), thereby providing capacity space exceeding the size of the columnar VM buffer.

#### Configuration and Management

- Capacity Configuration: 
    
    - The capacity of the column execution virtual memory is configured via the COLUMNAR_VM_BUFFER_SIZE parameter.
    
    - The allowed capacity of the SWAP tablespace for use is configured via the COLUMNAR_VM_SWAP_SIZE parameter.

- Performance Impact: When cache is insufficient, frequent swapping in and out will degrade performance.

- Tuning Guidance: Based on the size of column-store tables and query load, appropriately increase COLUMNAR_VM_BUFFER_SIZE. Based on system memory and available SWAP space, configure COLUMNAR_VM_SWAP_SIZE reasonably.

###  Stack Memory

#### Function Overview

Stack memory is divided into session stack memory and system stack memory:

- Session Stack Memory: Used to cache temporary objects generated during session execution.

- System Stack Memory: Used to cache function call information and temporary variable information.

#### Configuration and Management

- Capacity Configuration: The capacity of session stack memory is configured via the WORK_AREA_STACK_SIZE parameter. Adjustments to system stack memory capacity require contacting our technical support.

- Performance Impact:

    - Insufficient session stack memory may result in failed creation of temporary objects or memory overflow.

    - Insufficient system stack memory may cause stack overflow during function calls.

- Tuning Guidance: Configure appropriately based on the number of concurrent sessions and the complexity of execution.

###  Heap Memory

#### Function Overview

Heap memory is the session's execution-state memory pool, used to dynamically allocate and release temporary objects during SQL execution, such as intermediate results and temporary tables.

When a session's dedicated memory is insufficient during SQL execution, it can dynamically request memory from the [Global Application Pool](#GlobalApp) in the MEX pool, and the memory is automatically released after execution completes.

#### Configuration and Management

- Capacity Configuration: The capacity of heap memory is configured via the WORK_AREA_HEAP_SIZE parameter.

- Performance Impact: Insufficient heap memory may cause failures in creating temporary objects, affecting execution efficiency.

- Tuning Guidance: Configure WORK_AREA_HEAP_SIZE appropriately based on SQL execution complexity and number of concurrent sessions, and tune it in coordination with the Global Application Pool to avoid frequent cross-pool memory allocation.