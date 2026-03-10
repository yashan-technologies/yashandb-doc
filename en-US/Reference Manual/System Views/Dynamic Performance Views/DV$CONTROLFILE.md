This view displays the information of all current control files in the distributed system.

|Field |Type |Description |
| ------------------ | ------- | ---------- |
| GROUP_ID         | INTEGER | Group ID                  |
| GROUP_NODE_ID   | INTEGER | Node ID within the group   |
| ID                 | INTEGER | File number               |
| NAME               | VARCHAR(255) | File name (full path)   |
| BLOCK_SIZE       | INTEGER | Block size of the file (unit: bytes) |
| FILE_SIZE_BLKS  | INTEGER | Number of blocks in the file |
| BYTES              | BIGINT  | File size (unit: bytes)   |