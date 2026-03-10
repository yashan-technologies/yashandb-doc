This view displays the current thread information of all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER    | Group ID       |
| GROUP_NODE_ID  | INTEGER    | Node ID within group |
| NAME           | VARCHAR(32)| Thread name    |
| THREAD_ID      | BIGINT     | Thread number   |
| THREAD_ADDR    | BIGINT     | Thread address  |
| STACK_SIZE     | INTEGER    | Thread stack size (in bytes) |
| START_TIME     | TIMESTAMP  | Thread start time |
| STATUS         | VARCHAR(32)| Thread status   |