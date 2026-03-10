This view displays summary information of the session thread pool and login thread pool when opened in shared mode.

|Field |Type |Description |
|------------------|---------|---------------------|
| GROUP_ID           | NUMBER    | Group ID                     |
| GROUP_NODE_ID      | NUMBER    | Node ID within the group     |
| INST_ID            | NUMBER    | Instance ID                  |
| WORKER_ID          | SMALLINT  | Current worker ID            |
| THREAD_ID          | BIGINT    | Thread number used by worker  |
| QUEUE_ID           | INTEGER   | Queue ID used by current worker |
| POOL_ID            | INTEGER   | ID of the current worker pool |
| TASK_ADDR          | BIGINT    | Address of the task being executed by worker |
| GET_TASK_TIMES     | BIGINT    | Number of times worker fetched tasks from the queue (may fail) |
| GET_TASK_TIMEOUT_TIMES | BIGINT | Number of times worker timed out while fetching tasks from the queue |
| RUN_TASK_TIMES     | BIGINT    | Number of tasks that the worker has executed |
| STEAL_TASK_TIMES   | BIGINT    | Number of attempts to steal tasks from other task queues |
| RUN_STEAL_TASK_TIMES | BIGINT  | Number of tasks successfully stolen from other task queues |
| WORKER_START_TIME  | TIMESTAMP(6) | Time when the worker thread was created |
| LAST_ACTIVE_TIME   | TIMESTAMP(6) | Last active time of the worker |
| LAST_TASK_START_TIME | TIMESTAMP(6) | Time when the worker started executing the last registered task |
| LAST_TASK_END_TIME | TIMESTAMP(6) | Time when the worker finished executing the last registered task; NULL if currently not finished |
| MAX_TASK_RUN_TIME  | BIGINT    | Maximum time taken by the worker to execute a task (unit: milliseconds) |
| MIN_TASK_RUN_TIME  | BIGINT    | Minimum time taken by the worker to execute a task (unit: milliseconds) |
| AVG_TASK_RUN_TIME  | BIGINT    | Average execution time of tasks by the worker (unit: milliseconds) |