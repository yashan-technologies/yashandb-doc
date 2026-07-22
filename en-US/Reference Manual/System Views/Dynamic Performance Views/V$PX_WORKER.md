This view displays information about workers in the parallel worker pool.

|Field |Type |Description |
| ---                     | ---         | --- |
| NAME                     | VARCHAR(32)  | Worker name  |
| WORKER\_ID              | INTEGER     | worker ID |
| THREAD\_ID               | BIGINT       | Operating system thread ID |
| QUEUE\_ID                | INTEGER      | Worker task queue ID |
| POOL\_ID                 | INTEGER      | Worker pool ID |
| TASK\_ADDR               | BIGINT       | Address of the task being executed by the worker |
| STATUS                   | VARCHAR(8)   | Worker status<br>\* IDLE<br>\* IN USE |
| SID                      | SMALLINT     | Session ID, if a parallel task is running |
| SERIAL#                  | INTEGER      | Sequence number of the session |
| RUN\_TASK\_TIMES         | BIGINT       | Number of times parallel tasks have been executed |
| WORKER\_START\_TIME      | TIMESTAMP(6)    | Worker start time |
| LAST\_ACTIVE\_TIME       | TIMESTAMP(6)    | Last active time, used to judge if the thread is stuck |
| LAST\_TASK\_START\_TIME  | TIMESTAMP(6)    | Start time of the most recent parallel task executed |
| LAST\_TASK\_END\_TIME    | TIMESTAMP(6)    | End time of the most recent parallel task executed |
| MAX\_TASK\_RUN\_TIME     | BIGINT       | Longest execution time of all parallel tasks (unit: μs microseconds) |
| MIN\_TASK\_RUN\_TIME     | BIGINT       | Shortest execution time of all parallel tasks (unit: μs microseconds) |
| AVG\_TASK\_RUN\_TIME     | BIGINT       | Average execution time of all parallel tasks (unit: μs microseconds) |