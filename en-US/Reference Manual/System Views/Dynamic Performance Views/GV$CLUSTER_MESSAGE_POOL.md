This view shows an overview of YAC message pool.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                     |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                     |
| INST_ID       | NUMBER  | Instance ID                                  |
| ID            | INTEGER | Message pool ID                              |
| SIZE          | INTEGER | Size of message buffer units                 |
| BLOCK_COUNT   | INTEGER | Number of blocks currently used in the pool  |
| COUNT         | INTEGER | Number of messages the pool can store       |
| USE_COUNT     | INTEGER | Number of buffer units already allocated     |
| FREE_COUNT    | INTEGER | Number of buffer units allocated and released |
| WAIT_TIMES    | BIGINT  | Number of times waited when requesting memory |
| RECYCLE_TIMES | BIGINT  | Number of times other pools were evicted when requesting memory |