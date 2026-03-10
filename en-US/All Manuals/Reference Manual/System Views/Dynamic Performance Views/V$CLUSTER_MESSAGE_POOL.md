This view shows an overview of YAC message pool.

|Field |Type |Description |
| --- | --- | --- |
| ID | INTEGER | Message pool ID |
| SIZE | INTEGER | Size of message buffer units |
| BLOCK_COUNT | INTEGER | Number of blocks currently used by the message pool |
| COUNT | INTEGER | Number of messages that the message pool can store |
| USE_COUNT | INTEGER | Number of buffer units that have been allocated by the message pool |
| FREE_COUNT | INTEGER | Number of buffer units that have been released by the message pool |
| WAIT_TIMES | BIGINT | Number of times waited when requesting memory |
| RECYCLE_TIMES | BIGINT | Number of times other pools were evicted when requesting memory |