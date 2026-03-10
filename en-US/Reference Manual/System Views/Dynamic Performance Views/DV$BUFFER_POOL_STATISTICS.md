This view shows the statistics of the data buffer for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | INTEGER | Group ID                            |
| GROUP_NODE_ID      | INTEGER | Node ID within the group            |
| ID                 | INTEGER | Buffer partition number             |
| SIZE               | BIGINT  | Size of the buffer partition (in bytes) |
| NUM_TOTAL          | INTEGER | Total number of data blocks         |
| NUM_RESIDENT       | INTEGER | Number of resident data blocks      |
| NUM_MAIN           | INTEGER | Number of hot block chain data blocks |
| NUM_AUXILLIARY     | INTEGER | Number of auxiliary chain data blocks |
| NUM_WRITE          | INTEGER | Number of dirty page chain data blocks |
| NUM_TEMP           | INTEGER | Number of temporary chain data blocks |