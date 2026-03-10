This view displays detailed information about the global memory of various memory pools for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER    | Group ID                    |
| GROUP_NODE_ID  | INTEGER    | Node ID within the group    |
| POOL           | VARCHAR(32)| Memory pool type            |
| NAME           | VARCHAR(32)| Memory pool name            |
| BYTES          | BIGINT     | Memory pool size (in bytes) |