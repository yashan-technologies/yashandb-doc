This view displays the information of all current control files in the distributed system.

|Field |Type |Description |
| ------------------ | ------- | ---------- |
| GROUP\_ID         | INTEGER | Group ID                  |
| GROUP\_NODE\_ID   | INTEGER | Node ID within the group   |
| ID                 | INTEGER | File number               |
| NAME               | VARCHAR(255) | File name (full path)   |
| BLOCK\_SIZE       | INTEGER | Block size of the file (unit: bytes) |
| FILE\_SIZE\_BLKS  | INTEGER | Number of blocks in the file |
| BYTES              | BIGINT  | File size (unit: bytes)   |