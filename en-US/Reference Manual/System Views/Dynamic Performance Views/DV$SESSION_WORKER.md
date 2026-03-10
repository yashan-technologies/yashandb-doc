This view displays the summary information of distributed execution using the session worker pool across all nodes in a distributed cluster.

|Field |Type |Description |
|------------------|---------|---------------------|
| GROUP_ID          | INTEGER   | Group ID                     |
| GROUP_NODE_ID    | INTEGER   | Node ID within the group     |
| WORKER_ID         | SMALLINT  | Current worker ID            |
| THREAD_ID         | BIGINT    | Thread ID used by worker     |
| QUEUE_ID          | INTEGER   | Queue ID used by the current worker |
| POOL_ID           | INTEGER   | ID of the current worker pool |
| TASK_ADDR         | BIGINT    | Address of the task currently being executed by the worker |
| GET_TASK_TIMES   | BIGINT    | Number of times the worker has taken tasks from the queue (may fail) |
| GET_TASK_TIMEOUT_TIMES | BIGINT | Number of times the worker timed out without retrieving tasks from the queue |
| RUN_TASK_TIMES   | BIGINT    | Number of tasks executed by the worker |
| STEAL_TASK_TIMES | BIGINT    | Number of attempts to get tasks from other task queues |
| RUN_STEAL_TASK_TIMES | BIGINT | Number of tasks obtained from other task queues |
| WORKER_START_TIME | TIMESTAMP | Time when the worker thread was created |
| LAST_ACTIVE_TIME | TIMESTAMP | Last active time of the worker |
| LAST_TASK_START_TIME | TIMESTAMP | Time when the worker last started executing a task after registering it |
| LAST_TASK_END_TIME | TIMESTAMP | Time when the worker last finished executing a task after registering it; NULL if currently not finished |
| MAX_TASK_RUN_TIME | BIGINT | Maximum time for task execution by the worker (unit: microseconds) |
| MIN_TASK_RUN_TIME | BIGINT | Minimum time for task execution by the worker (unit: microseconds) |
| AVG_TASK_RUN_TIME | BIGINT | Average task execution time by the worker (unit: microseconds) |