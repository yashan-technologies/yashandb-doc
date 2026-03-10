This view displays information related to resource usage groups.

|Field |Type |Description |
| --- | --- | --- |
| ID | BIGINT | Resource group ID |
| NAME | VARCHAR(68) | Resource group name |
| SPA_LIMIT_QUOTA | BIGINT | Size of memory quota for the resource group (in bytes) |
| SESSION_SPA_LIMIT_QUOTA | BIGINT | Size of session memory quota (in bytes) |
| SPA_REMAIN_QUOTA | BIGINT | Remaining size of memory quota for the resource group (in bytes) |
| SPA_MAX_USE_QUOTA | BIGINT | Maximum size of memory usage for the resource group (in bytes) |
| SESSION_SPA_RESERVED_QUOTA | BIGINT | Reserved memory quota, no need to apply to the resource group within this range (in bytes) |
| SPA_LIMIT_EXCEED_TIMES | INTEGER | Number of times the resource group's memory has reached its limit |
| SESSION_SPA_LIMIT_EXCEED_TIMES | INTEGER | Number of times session memory usage has reached its limit |
| SESSION_SPA_MAX_USE_MEM | BIGINT | Maximum value of session memory usage (in bytes) |
| CONCURRENCY_LIMIT | INTEGER | Maximum concurrency configured for resource plans |
| CONCURRENCY_NUM | INTEGER | Current number of executing resource-intensive SQLs |
| EXECUTION_WAITERS | INTEGER | Resource-intensive SQLs waiting for execution |
| REQUESTS | INTEGER | Number of resource-intensive SQLs that have been executed in the resource group |
| QUEUE_NUMBER | INTEGER | Queue number value |
| CONCURRENCY_LIMIT_HIT | INTEGER | Number of times the number of concurrently executing resource-intensive SQLs has reached the limit |
| QUEUED_TIME | BIGINT | Total waiting time for execution of all sessions in the resource group |
| QUEUE_TIMEOUTS | INTEGER | Total number of session wait timeouts in the resource group |
| PARALLEL_DOWNGRADE_TIMES | INTEGER | Number of times parallel resource degradation occurred in the resource group |