This view shows information about all resource directives in the database.

|Field |Type |Description |
| --- | --- | --- |
| PLAN | VARCHAR(64)        | The name of the resource directive |
| GROUP_OR_SUBPLAN  | VARCHAR(64)   | The name of the resource usage group to which the directive belongs |
| TYPE    | VARCHAR(14)    | Indicates whether GROUP_OR_SUBPLAN is a resource usage group or a plan; currently only supports resource usage group |
| CPU_P1  | INTEGER        | Same meaning as field MGMT_P1 |
| CPU_P2  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P3  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P4  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P5  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P6  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P7  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| CPU_P8  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P1  | INTEGER      | The usage share in CPU shared mode |
| MGMT_P2  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P3  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P4  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P5  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P6  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P7  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MGMT_P8  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| ACTIVE_SESS_POOL_P1 | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| QUEUEING_P1  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| PARALLEL_TARGET_PERCENTAGE | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| PARALLEL_DEGREE_LIMIT_P1  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_GROUP | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_FOR_CALL | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_TIME  | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_IO_MEGABYTES | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_IO_REQS | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_ESTIMATE | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| MAX_EST_EXEC_TIME | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| UNDO_POOL | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MAX_IDLE_TIME | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MAX_IDLE_BLOCKER_TIME | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| MAX_UTILIZATION_LIMIT | INTEGER    | The maximum usage limit of CPU |
| PARALLEL_QUEUE_TIMEOUT | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_TIME_IN_CALL | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_IO_LOGICAL | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| SWITCH_ELAPSED_TIME | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| PARALLEL_SERVER_LIMIT | INTEGER    | The maximum percentage of parallel resources the resource usage group can use |
| UTILIZATION_LIMIT | INTEGER    | Same meaning as field MAX_UTILIZATION_LIMIT |
| PARALLEL_STMT_CRITICAL | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| SESSION_PGA_LIMIT | INTEGER    | For compatibility only; currently the value is fixed as `NULL` |
| PQ_TIMEOUT_ACTION | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| COMMENTS  | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| STATUS    | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| MANDATORY | VARCHAR(1)    | For compatibility only; currently the value is fixed as `NULL` |
| SPA_LIMIT | INTEGER    | The upper limit percentage of user memory occupied by the resource usage group (unit: %), public memory usage is not limited |
| SPA_LIMIT_RESERVED | INTEGER    | A certain percentage of user memory is reserved as private parts for each session within the resource usage group (unit: %). The user memory within the reserved range does not require an application to the resource usage group by sessions |
| SESSION_SPA_LIMIT | INTEGER    | The upper limit percentage of memory occupied by sessions in the resource group (unit: %) |
| EXECUTION_QUEUE_TIMEOUT | BIGINT    | The timeout period in seconds when waiting in the queue due to insufficient resources |
| CONCURRENCY_LIMIT | INTEGER    | The number of resource-intensive SQLs allowed to execute concurrently within the resource usage group |