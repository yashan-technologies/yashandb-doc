This view displays the statistics of the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER   | Group ID                                |
| GROUP_NODE_ID       | NUMBER   | Node ID within the group                |
| INST_ID             | NUMBER   | Instance ID                             |
| ID                  | INTEGER  | Buffer number                           |
| SIZE                | BIGINT   | Size of the buffer (unit: bytes)      |
| NUM_TOTAL           | INTEGER  | Total number of data blocks            |
| NUM_RESIDENT        | INTEGER  | Number of resident memory data blocks   |
| NUM_MAIN            | INTEGER  | Number of hot chain data blocks         |
| NUM_AUXILLIARY     | INTEGER  | Number of auxiliary chain data blocks   |
| NUM_WRITE           | INTEGER  | Number of dirty page chain data blocks  |
| NUM_TEMP            | INTEGER  | Number of temporary chain data blocks    |
| NAME                | VARCHAR(32) | Name of the cache, currently fixed as DEFAULT |
| SET_MSIZE           | BIGINT   | Maximum number of data blocks that can be set in the cache |
| BLOCK_SIZE          | BIGINT   | Size of data blocks managed in the cache pool |
| DB_BLOCK_GETS       | BIGINT   | Number of accesses to the latest version of data blocks from the cache |
| CONSISTENT_GETS     | BIGINT   | Number of consistent reads              |
| PHYSICAL_READS      | BIGINT   | Number of physical reads                |
| PHYSICAL_WRITES     | BIGINT   | Number of physical writes               |