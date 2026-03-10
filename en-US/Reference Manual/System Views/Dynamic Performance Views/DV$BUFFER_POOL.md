This view displays the basic information of the data buffers for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ID  | INTEGER | Buffer partition number |
| SIZE | BIGINT | Buffer partition size (in bytes) |
| BLOCK_SIZE | INTEGER | Data block size of the buffer partition (in bytes) |
| CHUNKS | INTEGER | Number of data chunks in the data block partition |
| BUFFERS | INTEGER | Number of data blocks in the data block partition |