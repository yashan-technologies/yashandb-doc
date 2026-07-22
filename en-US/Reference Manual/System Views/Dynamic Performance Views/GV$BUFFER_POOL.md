This view displays basic information about the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER  | Group ID                             |
| GROUP_NODE_ID  | NUMBER  | Node ID within the group             |
| INST_ID        | NUMBER  | Instance ID                          |
| ID             | INTEGER | Buffer partition number              |
| SIZE           | BIGINT  | Buffer partition size (in bytes)    |
| BLOCK_SIZE     | INTEGER | Data block size of the buffer partition (in bytes) |
| CHUNKS         | INTEGER | Number of data chunks in the data block partition |
| BUFFERS        | INTEGER | Number of data blocks in the data block partition |