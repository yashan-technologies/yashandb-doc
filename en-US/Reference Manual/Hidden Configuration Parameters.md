Hidden parameters are testing features, **to be used under the guidance of the original manufacturer engineers** and not considering compatibility. For more detailed hidden parameter information, you can query the [X$PARAMETER](./System Views/Dynamic Performance Views/X$PARAMETER.md) view.

|Parameter Name |Parameter Description |
|----------------------------------------------|-------------------------------------- |
| MAX_PARALLELISMS                             | Deprecated parameter.                             |
| MAX_PARALLELISMS_PER_EXEC                    | Deprecated parameter.                             |
| THREAD_STACK_SIZE                            | Size of the system stack memory.                   |
| PACKET_SIZE                                  | The size of communication packets between the client and server.                                       |
| _DATA_BUFFER_PARTS                           | The number of partitions in the data buffer.       |
| SQL_POOL_SIZE                                | The proportion of Share Pool used by the SQL Pool.                                |
| SQL_POOL_PARTS                               | The number of partitions in the SQL Pool.     |
| LOCK_POOL_SIZE                               | The size of the global lock resource pool in YAC/Distributed Cluster Deployment.                                       |
| _REPLICATION_BUFFER_SIZE                     | The size of the redo buffer.                       |
| _ARCHIVE_DELAY_TIME                          | The delay in archiving redo files.                |
| _HA_ELECTION_VOTE_FOR_PRE_CANDIDATE_INTERVAL | Tuning parameter for election framework for concurrent elections among multiple nodes, in milliseconds. |
| _HOT_CACHE_SIZE                              | The size of the Hot Cache.                                          |
| _SESSION_TEMP_TABLE_CACHE_SIZE               | The size of the buffer for global temporary table metadata per session.                             |
| VM_BUFFER_PARTS                              | The number of partitions in Virtual Memory space.                                               |
| VM_BUFFER_SWAP_TIMES                         | The multiple for swapping Virtual Memory to SWAP tablespace.                                       |
| DICTIONARY_CACHE_SIZE                        | The size of the dictionary cache.                 |
| _SESSION_RESERVED_CURSORS                    | The number of private cursors for each session, with private cursors preferred.                   |
| _SESSION_RESERVED_LOCKS                      | The number of private table locks for each session, session will prefer using private locks to store table lock information. |
| _UNDO_MAX_AUTOEXTEND_SEGMENTS                | The upper limit on the automatic extension of undo table space segments.                             |
| _UNDO_SEG_BUSY_TIMES                         | The number of wait times for undo segment latch locks; if the number exceeds this threshold within the detection interval, the SMON thread will automatically extend undo segments. |
| _XACT_UNDO_TIMES                             | The number of wait times to obtain transaction information; when the number exceeds this threshold within the detection interval, the SMON thread will automatically extend undo segments. |
| _WAIT_XA_RECOVER                             | In YAC/Distributed Cluster Deployment, whether DDL needs to retry internally until the XA transaction zone is restored after an instance failure; default is to retry. If retry is disabled, during the restoration of the XA transaction zone, DDL may report an error regardless of whether there are XA transactions in the zone, affecting normal DDL operations in YACs. <br/>**Modification is not recommended**. |
| _UNDO_SEG_EXTEND_INTERVAL                    | The time interval to trigger automatic extension of undo segment quantity, handled by the background SMON thread, and automatically extends the number of undo segments as needed. |
| _UNDO_FORCE_RETENTION                        | Enforce retention of committed transaction undo pages for a certain period.                          |
| _UNDO_RESERVE_SIZE | Percentage of data block (`DB_BLOCK_SIZE`) occupancy. The calculated size based on this percentage represents the minimum remaining space in the last undo data block used by a transaction that can be added to the free array.   |
| _PROCEDURE_LOCK_TIMEOUT                      | The timeout duration for waiting during stored procedure compilation, in seconds; equal to high value indicates infinite waiting. |
| _TEMP_BUFFER_PCT                             | The percentage of free pages in the temporary tablespace.                                          |
| _HOT_BUFFER_PCT                              | The proportion of high-priority data blocks in the data cache.                                       |
| _PROMOTE_NUMBER                              | Whether to promote to NUMBER when the return value type is BIGINT.                                    |
| DSTB_POOL_SIZE                               | In ISC Distributed Cluster Deployment, the proportion of memory pool to Share Pool.                             |
| AUDIT_SYS_OPERATIONS                         | Whether to enable logging of database operation behaviors to log files.                                 |
| _MCOL_SLICE_ROWS                             | The maximum number of data rows stored in each MCOL slice by LSC; ideally, not exceeding the set value for each slice. |
| _COLUMNAR_ROWGROUP_ROWS                      | Specifies the number of data rows in a rowgroup within a SCOL slice.                                 |
| COLUMNAR_MIN_OPERATOR_MEM_PERCENT            | The minimum memory allocation quota for column execution operators as a proportion of the total quota. |
| COLUMNAR_MAX_OPERATOR_MEM_PERCENT            | The maximum memory allocation quota for column execution operators as a proportion of the total quota. |
| _COLUMNAR_HIGH_PRIORITY_MEM_PERCENT          | The proportion of the allocation that can be used for high-priority memory in column execution STAGE quota. |
| _COLUMNAR_LOW_PRIORITY_MEM_PERCENT           | The proportion of the allocation that can be used for low-priority memory in column execution STAGE quota.  |
| _COLUMNAR_QUOTA_FACTOR                       | The proportion of the quota initially allocated for column execution SQL as a proportion of assessed memory. |
| _COLUMNAR_SQL_MIN_QUOTA_PERCENT              | The proportion of the minimum quota allocated for column execution SQL as a proportion of the total quota. |
| _COLUMNAR_SQL_MAX_QUOTA_PERCENT              | The proportion of the maximum quota allocated for column execution SQL as a proportion of the total quota. |
| COLUMNAR_MAX_STAGE_MEM_PERCENT               | The maximum quota allocated for column execution STAGE as a proportion of the total quota.                |
| COLUMNAR_MATERIAL_TRACE                      | Whether to enable logging of materialized quota information for column execution.                        |
| COLUMNAR_LOG_TRACE                           | Whether to print trace information to the log.                                                     |
| _COLUMNAR_ENABLE_HASH_GROUP_DISTINCT         | Whether to convert HASH GROUP DISTINCT of column execution into SORT GROUP DISTINCT.                    |
| COLUMNAR_MAX_SORT_MEM                        | Deprecated parameter.                               |
| COLUMNAR_MAX_JOIN_MEM                        | Deprecated parameter.                               |
| COLUMNAR_NON_COLLIDING_HASH                  | Whether to use the original key value as the HASH.                                                |
| _COLUMNAR_ENABLE_FILTER_INVALID              | Whether to filter out invalid row data in TABLESCAN during column execution.                           |
| _COLUMNAR_ENABLE_QUOTA_AUTOTRACE             | Whether to print QUOTA allocation information.                                                       |
| MAX_VM_OPEN                                  | The maximum number of Virtual Memory pages that can be opened simultaneously.                          |
| _CACHE_VARIABLE                              | Whether to enable the dynamic constant buffer.                                                       |
| _ENABLE_ALTER_SLICE                          | Whether to enable the ALTER SLICE capability.                                                       |
| _RWRT_OPT                                    | The possibility of performing static equivalence rewriting of SQL statements; the higher this value, the more likely rewriting will occur, conversely the less likely.  |
| _PUSH_FILTER | Whether to enable subquery pushdown during the static rewrite phase. |
| _ENABLE_EXPLAIN_STAGE                        | Whether to enable printing the operator's parallel execution information in the EXPLAIN plan.            |
| _ENABLE_EXPLAIN_STATS                        | Whether to enable printing detailed data such as row count and cost of operators in the EXPLAIN plan.   |
| _ENABLE_EXPLAIN_COST                         | Close/Open the printing of operator cost data in the EXPLAIN plan.                |
| _OPTIMIZER_MODE                              | Specify the optimizer mode.                                   |
| _OPTIMIZE_EXTEND_FILTER                      | Whether to enable the extension functionality of the filter support.                                    |
| _SPAREINDEX_OPT                              | Whether to generate a rangeSet based on the condition push down strategy for LSC tables.              |
| _TAC_FILTER_PUSH_THRESHOLD                   | The selection rate setting for TAC table condition push down; whether TAC is pushed down depends on this value and the calculated filter selection rate. |
| _OPTMZ_MINMAX_OPT                            | Specifies the optimization option for Index minmax scan by the optimizer. |
| _OPTMZ_EARLY_GROUP_OPT                       | Specifies the optimization option for Group by push down by the optimizer.                            |
| _OPTMZ_EXEC_ENGINE                           | Specifies the execution engine.                                                                     |
| _OPTMZ_ENABLE_DSTB_AC                        | Whether to enable support for AC tables in distributed mode.                                            |
| _OPTMZ_ENABLE_DUP_TABLE_PARALLEL             | Whether to enable parallel support for duplicated tables.                                               |
| _B_TREE_BITMAP_PLANS                         | Whether to enable the optimizer to generate BITMAP OR plans.                                          |
| _USED_PN_GROUP                               | Specifies the PN group in ISC Distributed Cluster Deployment.                                                   |
| _WITH_SUBQUERY                               | Instructs the optimizer to optimize CTE in a materialized/cost calculation/inlining manner.          |
| _OPTIMIZER_BATCH_TABLE_ACCESS_BY_ROWID       | Whether to enable the optimizer to generate bulk back-table plans.                       |
| _CONSISTENT_WRITE                            | Whether to enable consistent writing.                                                                 |
| DIN_CONNECTIONS_PER_NODE                     | In the internal communication network, the upper limit of socket connections that can be created between a single node and another node. |
| _SCOL_FILTER_MODE                            | LSC SCOL filter mode.                                                                                |
| _COAST_SIMD_MODE                             | The SIMD instruction set used by column execution storage.                                            |
| _SCOL_SLICE_LAYOUT                           | LSC SCOL slice layout.                                                                                |
| _STANDBY_MAX_FAILURE_TIMEOUT                 | In HA deployment and maximize protection mode, if the primary/standby disconnection exceeds the specified time of this parameter, it will be marked as abnormal. |
| _ENABLE_INTERNAL_ERROR_COREDUMP              | Indicates whether to allow the process to core dump when a fatal internal error occurs in the database under the release version. |
| _ENABLE_REDO_TRAFFIC_CONTROL                 | Start redo flushing flow control to prevent redo lags and improve the stability of database performance. |
| _BATCH_COMMIT_DELAY                          | When transactions are submitted in batch, this parameter controls the delay time for batch submission.  |
| _LATEST_RESET_ID                             | Used during yasom election.                                                                          |
| CGROUP_ROOT_DIR                              | The installation directory of cgroup.                 |
| _SCOL_MANAGER_CAPACITY                       | The capacity of the LSC SCOL file synchronization manager.                                           |
| _SCOL_EXTENT_SIZE                            | The size of the LSC SCOL slice extent; in cases of IO amplification, reduce this parameter when the actual query record is small but the scan volume is very large. |
| _MIGRATION_MODE_ENABLED                      | Database plug-and-play mode; the creation parameter that cannot be modified after creation; after configuring this parameter, all files must be stored in the $YASDB_DATA directory. |
| _PX_SENDER_PROJECT                           | Whether to perform projection transformation to the same data type when column execution has mismatched data types in PX upper and lower levels. |
| _MCOL_CACHE_REFRESH_SIZE                     | The memory range of block cache that stores MCOL slices in LSC; no adjustments needed.                |
| _PL_POOL_MEMORY_PERCENT                      |The proportion of the PL pool within the SQL Pool.                                       |
| _CHECK_PASSWORD_COMPLEXITY                   | Perform complexity checks when setting user passwords.                                              |
| _SQL_MAP                                     | Whether to enable SQL mapping functionality.                                                            |
| _REMOTE_CR_THRESHOLD                         | In YAC/Distributed Cluster Deployment, if a certain instance's remote request consistency read count for a page reaches this threshold, the request will be converted to the current page request. <br/>**This value affects performance across instances; modification is not recommended**. |
| _WAIT_BOC                                    | Whether to synchronize broadcast SCN during YAC transaction submission; default is TRUE. If off, it will automatically synchronize through the background periodically, which has a certain impact on consistency access on the application side. <br/>**Modification is not recommended**. |
| _RCY_BPWR_COUNT                              | In YAC/Distributed Cluster Deployment, the number of flush threads during page recovery when the instance is online, which affects the performance during fault recovery. <br/>**Modification is not recommended**.|
| _CLUSTER_MESSAGE_LOG_ENABLED                 | In YAC/Distributed Cluster Deployment, whether to log message-related runtime logs; default is OFF, for debugging by developers only. If enabled, it will log each message's type, size, source or target instance ID, etc., which may reduce system performance and have information security risks. <br/>**Modification is not recommended**. |
| _BUDDY_INSTANCE_MEMORY                       | In YAC/Distributed Cluster Deployment, the percentage of memory allocated for the BUDDY INSTANCE from the free SHARE_POOL.   |
| _DB_BLOCK_MAX_CR_DBA                         | The maximum number of consistency read pages for a certain page in the DATA BUFFER buffer. <br/>**This value affects performance; modification is not recommended**. |
| _REDO_FLUSH_TRIGGERED_DELAY                  | In YAC/Distributed Cluster Deployment, the wait time for log flushing triggered by the global cache service thread. <br/>**This value affects performance; modification is not recommended**. |
| _ENABLE_CREATE_AC_BTREE                      | Deprecated parameter.                             |
| _TWO_PHASE_FETCH_THRESHOLD                   | The proportion of pushed columns to projected columns; reaching this value will enable two-phase fetch in SWF. |
| _COL_MEM_POOL_EVICT_ENABLED                  | Whether to enable eviction mechanism for memory pools.                                              |
| _COL_RUNTIME_FILTER_USE_PRECISE_ROWS         | The maximum number of DISTINCT values used by the runtime filter in column execution.                |
| _SLOW_LOG_QUEUE_SIZE                         | Specifies the length of the current waiting queue for slow query logs.                              |
| _SLOW_LOG_EPOCH_SIZE                         | Specifies the length of the current waiting period for slow query logs.                             |
| _ENABLE_DEADLOCK_PARAM                        | Specifies whether to enable printing of deadlock log binding parameters.                            |
| _ENABLE_OLD_DECIMAL                          | Whether to be compatible with reading the old version of the decimal type.                          |
| _CLUSTER_ID                                  | Global unique ID for the distributed cluster.                                                     |
| _SPLIT_BRAIN_DATA_LIMIT                      | Data limit for split brain repair; when the repaired pages exceed this value, the efficiency of split brain repair will be poor, recommending rebuilding the standby database. |
| _DATA_TRANSFORMER_MAX_TASK_COUNT             | The maximum number of tasks scheduled for XFMR.                                                  |
| _DATA_TRANSFORMER_SCHEDULING_TIME            | The scheduling time for XFMR tasks.                                                                |
| _SCOL_RGD_BUFFER_COUNT                       | Deprecated parameter.                             |
| _SCOL_RGD_COUNT                              | Deprecated parameter.                             |
| _SCOL_RGD_MAX_RESERVE_WRITER                 | Deprecated parameter.                             |
| _SCOL_DELTASLICE_BUFFER_COUNT                | The number of buffers for a single incremental slice in the LSC table SCOL.                       |
| _SCOL_DELTASLICE_COUNT                       | The number of incremental slices under a single partition in the LSC table.                       |
| _SCOL_DELTABUFFER_MAX_RESERVE_WRITER         | The maximum number of slice writers available for incremental data in the LSC table.               |
| COLUMNAR_BULK_COUNT                          | Used to configure the upper limit of how many batches of data will be used as one sorting step during column execution. |
| _BUCKET_VALUE_SCALE_IN_HASH_SET              | The number of elements in the BUCKET of the HASH SET during column execution.                      |
| _BUCKET_VALUE_SCALE_IN_HASH_TABLE            | The number of elements in the BUCKET of the HASH TABLE during column execution.                    |
| _COLUMNAR_MAX_BATCH_COUNT                    | The maximum batch count for column execution.                                                    |
| _COLUMNAR_MAX_HASH_BUCKET                    | The initial maximum number of BUCKETs in the column execution HASH TABLE.                        |
| _COLUMNAR_DYNAMIC_ARRAY_THRESHOLD            | The threshold for using two-dimensional dynamic arrays in column execution.                         |
| _USE_OPTMZ_INFO                              | Deprecated parameter.                             |
| _COST_VM_BUFFER_SIZE                         | Used only as a memory parameter for planning adjustments. When this parameter is set, the value of this parameter (not VM_BUFFER_SIZE) is used to evaluate the available memory adjustment plan. |
| _COST_DATA_BUFFER_SIZE                       | Used only as a memory parameter for planning adjustments. When this parameter is set, the value of this parameter (not DATA_BUFFER_SIZE) is used to evaluate the available memory adjustment plan. |
| _COST_MEX_POOL_SIZE                          | Used only as a memory parameter for planning adjustments. When this parameter is set, the value of this parameter (not MEX_POOL_SIZE) is used to evaluate the available memory adjustment plan. |
| _FAULT_POINT                                 | Fault injection content, effective only in the Debug version.                                     |
| _DBWR_SORT_ENABLED                           | Page sorting switch during DBWR flush.                                                            |
| _ENABLE_S3                                   | Whether to allow operations on s3 bucket.                                                         |
| _ARCH_DATA_THRESHOLD                         | The maximum number of records retained in the ARCH_DATA$ system table.                             |
| _ARCH_AUTO_CELAN_DATA_NUM                    | The batch size for slice file archival cleanup.                                                   |
| _CHN_MAX_SEGMENT_SIZE                        | Specifies the maximum size of message segments used for data transfer between parallel threads. <br/>**Modification is not recommended**.|
| _CRYPTO_ENABLED                              | Whether to allow not loading the openssl library.                                                |
| _CN_SCALING_OUT                              | Deprecated parameter.                             |
| _ONLY_OPEN_UPGRADE                           | During the upgrade process, the database is not allowed to switch from nomount to open.              |
| _REDO_FLUSH_THRESHOLD_SIZE                   | The triggering condition for the background redo flushing; when the redo size exceeds this value, it will automatically trigger flushing. |
| _HASH_JOIN_ALGORITHM                         | The algorithm used for HASH JOIN.               |
| _HASH_JOIN_FORCE_PARTITION                   | During vectorized HASH JOIN, whether to force open partition. If true, it indicates forcing partition. <br/>**Not recommended for production environments**. |
| _HASH_AREA_SIZE                              | The maximum memory size available for each HASH JOIN. When the HASH TABLE size is less than this value, partition will not execute, and the entire HASH TABLE will be placed in memory. |
| DERM_PARALLEL_EXECUTIONS                     | The number of distributed parallel execution threads.                                               |
| DERM_ENABLED                                 | Whether to enable distributed resource management.                                               |
| CONTROL_ADDR                                 | The management plane service address for this node. If the node's external address or port changes, it needs to be modified accordingly. |
| _ENABLE_DISTRIBUTE_TRANSACTION               | Whether to enable distributed transactions.                                                       |
| _ENABLE_INDEX                                | Whether to support creating indexes.                                                                |
| _ENABLE_HEAP                                 | Whether to support creating HEAP tables.                                                            |
| _ENABLE_TAC                                  | Whether to support creating TAC tables.                                                             |
| _ENABLE_LSC                                  | Whether to support creating LSC tables.                                                             |
| DS_SCALE_OUT_FACTOR                          | Specifies the default number of shards for each node group when creating data space.                |
| _SCOL_ENABLE_AUTO_ENCODING                   | Whether to enable adaptive encoding in column execution.                                             |
| PASSWORD_HASH_METHOD                         | The password verification method and the encryption method used when storing passwords on the server.      |
| PASSWORD_LOGON_MIN_VERSION                   | Used in conjunction with the PASSWORD_HASH_METHOD parameter to specify the minimum version of the supported HASH algorithm.   |
| PASSWORD_CRYPTO_METHOD                       | The encryption method used during password authentication.    |
| CLIENT_UKEY_AUTH                             | Whether the client uses UKEY for authentication login.                                             |
| _HA_ELECTION_AUTO_PRIMARY_SWITCH_INTERVAL    | The detection interval for automatic primary switch.                                                |
| _SESSION_CACHE_BLOCKS                        | The maximum number of pages each session can buffer.                                              |
| _SESSION_CACHE_TABLES                        | The maximum number of table locks each session can buffer.                                        |
| _SESSION_BLOCK_CACHE_THRESHOLD               | The number of shared lock holders that triggers the session-level buffer page; when requesting a page's shared lock, if the number of shared lock holders has reached this parameter, it will buffer the page into the session. |
| _BATCH_ENABLED                               | Deprecated parameter.                             |
| _BATCH_SIZE                                  | Specifies the number of rows in a batch for executing batch operations.                             |
| _BATCH_MODE                                  | Specifies the execution mode for batch operations. |
| _CURSOR_BIND_CAPTURE_INTERVAL                | The interval for capturing specific values of bound parameters in SQL records (units: seconds). |
| ENABLE_TRAFFIC_CONTROL                       | Whether to enable the DATA BUFFER flow control mechanism; default is off. If enabled, it will limit the DATA BUFFER to prevent dirty pages from being produced too quickly. |
| _UNDO_AFFINITY                               | Whether to enable UNDO affinity functionality in cluster deployment mode.                            |
| _ENABLE_BOM                                  | Whether to enable object cache management functionality; currently, column storage does not support this functionality. |
| _INDEX_NOPARALLEL                            | Whether to enable the default parallel functionality for index creation.                            |
| _BUFFER_OBJECTS                              | When enabling object cache management functionality, the maximum number of cache objects that can be managed. |
| _PARALLEL_ADAPTIVE_MULTI_USER                | Whether to enable adaptive algorithms to reduce parallelism when parallel execution resources are insufficient. |
| _PARALLEL_DOP_LOWER_BOUND                    | After enabling the adaptive algorithm to adjust parallelism, the lower bound for the adjustment of parallelism when resources are insufficient will be set to parallelism/_PARALLEL_DOP_LOWER_BOUND. The range of _PARALLEL_DOP_LOWER_BOUND is [1, 16]. |
| _SCOL_MAX_DICTIONARY_CARDINALITY             | Sets the maximum cardinality of the dictionary-encoded column for cold data in LSC tables, range [1, 16M]. |
| _BATCH_ERROR_PROCESS_SIZE                    | The memory upper limit for a single batch process in batchError mode for DML; increasing this will correspondingly increase the batch processed at once. |
| _COLUMNAR_EXPAND_ARRAY_THRESHOLD             | The memory threshold for the vectorized execution engine to expand from one-dimensional dynamic arrays to two-dimensional arrays during execution of HASH GROUP/GROUPING/JOIN/SET operators. |
| _HASH_GROUP_HLL_ENABLED                      | Whether to enable HLL algorithm for counting distinct values in Hash Group.                         |
| _COLUMNAR_DATA_BUFFER_PERCENT_MIN            | The lower limit of COLUMNAR_DATA_BUFFER_PERCENT as a proportion of total memory.                      |
| _COLUMNAR_DATA_BUFFER_PERCENT_MAX            | The upper limit of COLUMNAR_DATA_BUFFER_PERCENT as a proportion of total memory.                      |
| _COLUMNAR_BUFFER_BALANCER_CYCLE              | The execution cycle of the BALANCER; the smaller the time, the higher the sensitivity, which may lead to oscillation. Automatic adjustment is triggered by the stable deviation of multiple cycles. |
| _COLUMNAR_BUFFER_BALANCER_DEVIATION          | The error ratio of cost adjustment calculated by the BALANCER.                                       |
| _COLUMNAR_BUFFER_HOT_HIT_LIMIT               | Counting the number of hits for hot data in LSC buffer related to COST.                             |
| _SCHEDULE_STAGE_THRESHOLD                    | The minimum number of STAGES required to start STAGE scheduling in ISC Distributed Cluster Deployment.            |
| _PASSWORD_MIN_LENGTH                         | The minimum length of a database user's password; this parameter only takes effect after enabling _CHECK_PASSWORD_COMPLEXITY. |
| _CONS_NOPARALLEL                             | Whether to enable the default parallel functionality for creating check constraints.                 |
| _ROLLING_UPGRADE                             | This parameter is automatically set by the upgrade tool during a rolling upgrade between incompatible versions, indicating that a database rolling upgrade is in progress to intercept operations that should not be executed during the upgrade. |
| _SCOL_SLICE_MERGE_THRESHOLD                  | When the number of slices for partition or single table data is less than 128K, force a merge upon reaching the upper limit. |
| _SCOL_ALL_LEVEL_COMPACT_WAIT_TIME            | The maximum time without new slices being generated before triggering a merge of slices at different size levels. |
| _TRANSFORMER_MAX_HIS_COUNT                   | The maximum number of records that the XFMR_HIS$ system table can retain.                           |
| _TRANSFORMER_MAX_HIS_DAYS                    | The maximum interval in days that the XFMR_HIS$ system table can retain records.                    |
| _TRACE_ALLOC_CURSOR                          | A reserved parameter for checking resource allocation; modification is not allowed.                  |
| _INSTANCE_JOIN_TIMEOUT                       | The timeout duration for an instance to join the cluster (units: seconds); default value is 0, representing a dead wait. |
| _ENABLE_SYNC_GB_PURGE                        | Whether to enable GB synchronous purge.                 |
| _CHANNEL_INIT_WINDOW                         | Specifies the size of the channel initialization window for data transfer across nodes.               |
| _EXEC_DEGREE_OF_PARALLEL                     | Deprecated parameter.                             |
| DP_MAX_JOIN_TABLES                           | Controls the number of tables supported by the DP algorithm; if the number of tables joined in SQL statement exceeds this value, a greedy algorithm will be used, which may result in a suboptimal plan. If less than or equal to this value, the DP algorithm will be used, resulting in an optimal plan but higher memory overhead.  |
| PKCS11_MODULE_LOCATION                       | Provide the module path of the standard PKCS #11 interface implementation to enable key management using an external HSM. |
| _WINFUNC_ALGORITHM                           | Specify the aggregation algorithm for bulk execution of window functions.  |
| _BATCH_WINDOW_TOPN_LIMIT                    | Specify the maximum N value supported by the bulk execution window function TOP N algorithm. If set to 0, the bulk execution window function TOPN algorithm is disabled. |
| _GCS_LOCK_RATIO                            | In YAC/Distributed Cluster Deployment, the ratio of GcsLock count to data block cache count, i.e., GcsLock count = data block cache count * _GCS_LOCK_RATIO. |
| _GCS_LOCK_RECYCLE_PROCS                      | In YAC/Distributed Cluster Deployment, the number of background asynchronous threads for reclaiming orphaned GcsLocks. |
| _GCS_LOCK_ASYNC_RECYCLE                      | In YAC/Distributed Cluster Deployment, whether to perform GcsLock eviction and data block cache eviction asynchronously. If asynchronous eviction is enabled, GcsLocks are unbound as much as possible during data block eviction and evicted asynchronously by background threads. If not enabled, GcsLocks are evicted along with data block cache.   |
| _TASK_QUEUE_TIMEOUT                          | When shared thread session mode is enabled, the timeout time for judging whether the task queue is in a stuck state, in milliseconds. |
| _OPTIMIZER_ADAPTIVE_CURSOR_SHARING | Controls whether different parameter value ranges affect plan generation; default is `FALSE`. |
| _HASH_CACHE_THRESHOLD                      | The maximum cache size for the hash table optimized for batch execution of deterministic UDFs. Default value: 4096. The actual maximum cache size depends on the data distribution and ranges between [parameter_value, parameter_value + 1024]. |