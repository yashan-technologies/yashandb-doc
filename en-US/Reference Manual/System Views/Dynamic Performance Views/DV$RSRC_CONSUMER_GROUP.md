This view shows the relevant information about resource usage groups for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                  | INTEGER  | Group ID                                                           |
| GROUP_NODE_ID             | INTEGER  | Node ID within the group                                           |
| ID                        | BIGINT   | Resource group ID                                                  |
| NAME                      | VARCHAR(68) | Resource group name                                                |
| SPA_LIMIT_QUOTA          | BIGINT   | Resource group memory quota size (unit: bytes)                   |
| SESSION_SPA_LIMIT_QUOTA  | BIGINT   | Session memory quota size (unit: bytes)                           |
| SPA_REMAIN_QUOTA         | BIGINT   | Remaining memory quota size for the resource group (unit: bytes)  |
| SPA_MAX_USE_QUOTA        | BIGINT   | Maximum memory usage quota size for the resource group (unit: bytes) |
| SESSION_SPA_RESERVED_QUOTA | BIGINT   | Reserved memory quota, within this range no need to apply to the resource group (unit: bytes) |
| SPA_LIMIT_EXCEED_TIMES   | INTEGER  | Number of times memory limit exceeded in the resource group       |
| SESSION_SPA_LIMIT_EXCEED_TIMES | INTEGER  | Number of times session memory usage reached the limit             |
| SESSION_SPA_MAX_USE_MEM  | BIGINT   | Maximum session memory usage (unit: bytes)                        |
| CONCURRENCY_LIMIT        | INTEGER  | Maximum concurrency configured for resource scheduling              |
| CONCURRENCY_NUM          | INTEGER  | Current number of executing resource-intensive SQLs                |
| EXECUTION_WAITERS        | INTEGER  | Resource-intensive SQLs waiting for execution                      |
| REQUESTS                 | INTEGER  | Number of resource-intensive SQLs executed in the resource group   |
| QUEUE_NUMBER             | INTEGER  | Queue number value                                                 |
| CONCURRENCY_LIMIT_HIT    | INTEGER  | Number of times the limit for concurrently executing resource-intensive SQLs was hit |
| QUEUED_TIME              | BIGINT   | Cumulative waiting execution time for all sessions in the resource group |
| QUEUE_TIMEOUTS           | INTEGER  | Cumulative number of scheduling timeouts for sessions in the resource group |
| PARALLEL_DOWNGRADE_TIMES | INTEGER  | Number of times parallel resource downgrades occurred in the resource group |