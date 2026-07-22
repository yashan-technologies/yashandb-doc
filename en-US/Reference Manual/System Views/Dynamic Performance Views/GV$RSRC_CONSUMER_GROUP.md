This view displays information related to resource usage groups.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| ID | BIGINT | Resource group ID |
| NAME | VARCHAR(68) | Resource group name |
| SPA_LIMIT_QUOTA | BIGINT | Memory quota size for the resource group (unit: bytes) |
| SESSION_SPA_LIMIT_QUOTA | BIGINT | Memory quota size for the session (unit: bytes) |
| SPA_REMAIN_QUOTA | BIGINT | Remaining memory quota size for the resource group (unit: bytes) |
| SPA_MAX_USE_QUOTA | BIGINT | Maximum memory usage quota size for the resource group (unit: bytes) |
| SESSION_SPA_RESERVED_QUOTA | BIGINT | Reserved memory quota, within this range no application to the resource group is needed (unit: bytes) |
| SPA_LIMIT_EXCEED_TIMES | INTEGER | Number of times memory limit has been reached in the resource group |
| SESSION_SPA_LIMIT_EXCEED_TIMES | INTEGER | Number of times session memory usage has reached its limit |
| SESSION_SPA_MAX_USE_MEM | BIGINT | Maximum session memory usage (unit: bytes) |
| CONCURRENCY_LIMIT | INTEGER | Maximum concurrency configured for the resource plan |
| CONCURRENCY_NUM | INTEGER | Current number of executing resource-intensive SQLs |
| EXECUTION_WAITERS | INTEGER | Resource-intensive SQLs waiting for execution |
| REQUESTS | INTEGER | Number of resource-intensive SQLs already executed in the resource group |
| QUEUE_NUMBER | INTEGER | Queue number value |
| CONCURRENCY_LIMIT_HIT | INTEGER | Number of times the maximum number of concurrently executing resource-intensive SQLs has been reached |
| QUEUED_TIME | BIGINT | Total waiting execution time of all sessions in the resource group |
| QUEUE_TIMEOUTS | INTEGER | Total number of scheduling timeouts for sessions in the resource group |
| PARALLEL_DOWNGRADE_TIMES | INTEGER | Number of occurrences of parallel resource downgrades in the resource group |