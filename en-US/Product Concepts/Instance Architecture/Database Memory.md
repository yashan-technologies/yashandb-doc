YashanDB database consists of multiple memory areas, each containing multiple subcomponents.

- SGA (SGA, Shared Global Area)

    SGA is a set of memory structures that can be shared by all background threads and sessions, such as data buffer, SQL buffer, data dictionary buffer, etc.

- SPA (SPA, Session Private Area)

    SPA is a memory area exclusively allocated and managed by the session, such as session stack memory and session heap memory.

## SGA (SGA, Shared Global Area)

SGA is generally shared by multiple sessions or threads and mainly includes the shared memory pool (SHARE POOL), data buffer (DATA BUFFER), bounded acceleration buffer (AC BUFFER), and virtual memory (VIRTUAL MEMORY).

- Shared memory pool (SHARE POOL): The shared memory pool buffers various types of information, such as SQL parse trees, execution plans, and data dictionary buffers.

- Data buffer (DATA BUFFER): The data buffer is used to accelerate data access. If the accessed data block is not found in the buffer, it must be read from the disk into the buffer. When buffer usage is too high, some seldom-used data blocks will be evicted.

- Bounded acceleration buffer (AC BUFFER): The bounded acceleration buffer is similar to the data buffer, but its objects differ; it is used solely for buffering AC objects.

### Shared memory pool (SHARE POOL)

The shared memory pool consists of multiple memory areas, each described as follows:

- SQL buffer: Stores SQL parse trees and execution plans. When the SQL engine executes a statement, it first checks the SQL buffer; if an identical statement exists, it uses the already compiled execution plan to avoid hard parsing and save overhead.

- Log buffer: Buffer for redo logs, allows concurrent writing of logs to the buffer, and batches flushing to disk, thereby reducing disk I/O and minimizing response latency.

- PL pool: After creation, objects like stored procedures, packages, and triggers are loaded into the PL pool to improve the efficiency of subsequent executions.

- Data dictionary buffer: The data dictionary contains information about database files, tables, indexes, columns, users, privileges, and other database objects. During SQL parsing and execution, the SQL engine checks the data dictionary to validate and verify object information. The data dictionary is frequently used, and accessing it through the buffer can significantly enhance efficiency.

- Large object pool: Area allocated for large objects, such as very large SQL texts.

- Global buffer resource pool: Stores global resource metadata information for YAC databoxes.

- Global lock resource pool: Stores global lock-related metadata information for YAC.

- Global queue resource pool: Stores management information for queuing global buffers and locks in YAC.

- STREAM resource pool: Stores metadata, LCR buffers, message queues, and other information during YStream parsing.

### Data Buffer (DATA BUFFER)

The data buffer is used to buffer copies of data blocks read from disk that are currently or recently in use, optimizing database I/O and reducing physical reads/writes. It is managed using the LRU algorithm; when buffer memory is insufficient and needs to be reclaimed for reuse, it selects some of the least recently used buffer blocks for eviction.

Data buffers are divided into row data buffers and column data buffers. Row data buffers store copies of data blocks related to row tables, while column data buffers store copies of data blocks related to column tables.

### Bounded Acceleration Buffer (AC BUFFER)

The bounded acceleration buffer is similar to the data buffer, but its objects differ; it exclusively stores AC objects based on bounded theory.

## SPA (SPA, Session Private Area)

SPA is an exclusive memory area allocated when each session is created. Unlike SGA, this memory is owned and managed exclusively by the session and is released when the session exits. This memory area primarily meets various memory space needs during SQL execution. It consists of:

- Virtual memory (VIRTUAL MEMORY): Also known as Materialize zone, virtual memory is mainly used by SQL operators that require materialized data, and when materialized objects are too large, disk space is utilized as virtual memory. Virtual memory is further divided into row virtual memory and column virtual memory; the related SQL operators use row virtual memory when processing heap table data and use column virtual memory when processing LSC table data.

- Session stack memory: This area is generally used to store temporary local variables used during session execution.

- Session heap memory: This area is generally used to store runtime data with a longer lifecycle.
