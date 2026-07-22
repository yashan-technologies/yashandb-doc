This view displays the summary information of distributed execution using the session worker pool across all nodes in an ISC distributed cluster.

|Field |Type |Description |
|------------------|---------|---------------------|
| GROUP\_ID          | INTEGER   | Group ID                     |
| GROUP\_NODE\_ID    | INTEGER   | Node ID within the group     |
| WORKER\_ID         | SMALLINT  | Current worker ID            |
| THREAD\_ID         | BIGINT    | Thread ID used by worker     |
| QUEUE\_ID          | INTEGER   | Queue ID used by the current worker |
| POOL\_ID           | INTEGER   | ID of the current worker pool |
| TASK\_ADDR         | BIGINT    | Address of the task currently being executed by the worker |
| GET\_TASK\_TIMES   | BIGINT    | Number of times the worker has taken tasks from the queue (may fail) |
| GET\_TASK\_TIMEOUT\_TIMES | BIGINT | Number of times the worker timed out without retrieving tasks from the queue |
| RUN\_TASK\_TIMES   | BIGINT    | Number of tasks executed by the worker |
| STEAL\_TASK\_TIMES | BIGINT    | Number of attempts to get tasks from other task queues |
| RUN\_STEAL\_TASK\_TIMES | BIGINT | Number of tasks obtained from other task queues |
| WORKER\_START\_TIME | TIMESTAMP(6) | Time when the worker thread was created |
| LAST\_ACTIVE\_TIME | TIMESTAMP(6) | Last active time of the worker |
| LAST\_TASK\_START\_TIME | TIMESTAMP(6) | Time when the worker last started executing a task after registering it |
| LAST\_TASK\_END\_TIME | TIMESTAMP(6) | Time when the worker last finished executing a task after registering it; NULL if currently not finished |
| MAX\_TASK\_RUN\_TIME | BIGINT | Maximum time for task execution by the worker (unit: microseconds) |
| MIN\_TASK\_RUN\_TIME | BIGINT | Minimum time for task execution by the worker (unit: microseconds) |
| AVG\_TASK\_RUN\_TIME | BIGINT | Average task execution time by the worker (unit: microseconds) |