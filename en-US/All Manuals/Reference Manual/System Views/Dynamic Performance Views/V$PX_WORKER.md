This view displays information about workers in the parallel worker pool.

|Field |Type |Description |
| ---                     | ---         | --- |
| NAME                     | VARCHAR(32)  | Worker name  |
| WORKER_ID              | INTEGER     | worker ID |
| THREAD_ID               | BIGINT       | Operating system thread ID |
| QUEUE_ID                | INTEGER      | Worker task queue ID |
| POOL_ID                 | INTEGER      | Worker pool ID |
| TASK_ADDR               | BIGINT       | Address of the task being executed by the worker |
| STATUS                   | VARCHAR(8)   | Worker status<br>\* IDLE<br>\* IN USE |
| SID                      | SMALLINT     | Session ID, if a parallel task is running |
| SERIAL#                  | INTEGER      | Sequence number of the session |
| RUN_TASK_TIMES         | BIGINT       | Number of times parallel tasks have been executed |
| WORKER_START_TIME      | TIMESTAMP(6)    | Worker start time |
| LAST_ACTIVE_TIME       | TIMESTAMP(6)    | Last active time, used to judge if the thread is stuck |
| LAST_TASK_START_TIME  | TIMESTAMP(6)    | Start time of the most recent parallel task executed |
| LAST_TASK_END_TIME    | TIMESTAMP(6)    | End time of the most recent parallel task executed |
| MAX_TASK_RUN_TIME     | BIGINT       | Longest execution time of all parallel tasks (unit: μs microseconds) |
| MIN_TASK_RUN_TIME     | BIGINT       | Shortest execution time of all parallel tasks (unit: μs microseconds) |
| AVG_TASK_RUN_TIME     | BIGINT       | Average execution time of all parallel tasks (unit: μs microseconds) |