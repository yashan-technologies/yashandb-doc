This view displays summary information using the session worker pool when in shared mode.

|Field |Type |Description |
|------------------|---------|---------------------|
| WORKER_ID         | SMALLINT  | Current worker ID                     |
| THREAD_ID         | BIGINT    | Thread number used by the worker      |
| QUEUE_ID          | INTEGER   | Queue ID currently used by the worker |
| POOL_ID           | INTEGER   | ID of the current worker pool         |
| TASK_ADDR         | BIGINT    | Address of the task currently being executed by the worker |
| GET_TASK_TIMES    | BIGINT    | Number of times the worker fetched a task from the queue (may fail) |
| GET_TASK_TIMEOUT_TIMES | BIGINT | Number of times the worker timed out while trying to fetch a task from the queue |
| RUN_TASK_TIMES    | BIGINT    | Number of tasks executed by the worker |
| STEAL_TASK_TIMES  | BIGINT    | Number of attempts to steal tasks from other task queues |
| RUN_STEAL_TASK_TIMES | BIGINT  | Number of tasks successfully stolen from other task queues |
| WORKER_START_TIME  | TIMESTAMP | Time when the worker thread was created |
| LAST_ACTIVE_TIME   | TIMESTAMP | Last active time of the worker        |
| LAST_TASK_START_TIME | TIMESTAMP | Time when the worker last started executing a task after registering it |
| LAST_TASK_END_TIME | TIMESTAMP | Time when the worker last ended executing a task after registering it; NULL if currently not ended |
| MAX_TASK_RUN_TIME  | BIGINT    | Longest time the worker has executed a task (in milliseconds) |
| MIN_TASK_RUN_TIME  | BIGINT    | Shortest time the worker has executed a task (in milliseconds) |
| AVG_TASK_RUN_TIME  | BIGINT    | Average execution time of tasks by the worker (in milliseconds) |