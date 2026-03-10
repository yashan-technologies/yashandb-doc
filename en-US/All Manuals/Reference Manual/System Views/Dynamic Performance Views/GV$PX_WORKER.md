This view displays information about workers in the parallel worker pool.

|Field |Type |Description |
| ---                     | ---         | --- |
| GROUP_ID               | NUMBER       | Group ID |
| GROUP_NODE_ID          | NUMBER       | Node ID within the group |
| INST_ID                | NUMBER       | Instance ID |
| NAME                   | VARCHAR(32)  | Worker name |
| WORKER_ID              | INTEGER     | worker ID |
| THREAD_ID              | BIGINT       | Operating system thread ID |
| QUEUE_ID               | INTEGER      | Task queue ID of the worker |
| POOL_ID                | INTEGER      | Worker pool ID |
| TASK_ADDR              | BIGINT       | Address of the task being executed by the worker |
| STATUS                 | VARCHAR(8)   | Worker status<br>* IDLE<br>* IN USE |
| SID                    | SMALLINT     | Session ID if there are parallel tasks in execution |
| SERIAL#                | INTEGER      | Sequence number of the session |
| RUN_TASK_TIMES         | BIGINT       | Number of times parallel tasks have been executed |
| WORKER_START_TIME      | TIMESTAMP    | Worker start time |
| LAST_ACTIVE_TIME       | TIMESTAMP    | Last active time, used to determine if the thread is stuck |
| LAST_TASK_START_TIME   | TIMESTAMP    | Start time of the most recent parallel task execution |
| LAST_TASK_END_TIME     | TIMESTAMP    | End time of the most recent parallel task execution |
| MAX_TASK_RUN_TIME      | BIGINT       | Longest execution time among all parallel tasks (unit: μs microseconds) |
| MIN_TASK_RUN_TIME      | BIGINT       | Shortest execution time among all parallel tasks (unit: μs microseconds) |
| AVG_TASK_RUN_TIME      | BIGINT       | Average execution time among all parallel tasks (unit: μs microseconds) |