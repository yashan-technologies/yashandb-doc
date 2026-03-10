This view shows the statistics of the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | The identifier of the buffer partition |
| SIZE | BIGINT | The size of the buffer partition (in bytes) |
| NUM_TOTAL | INTEGER | Total number of data blocks |
| NUM_RESIDENT | INTEGER | Number of resident memory data blocks |
| NUM_MAIN | INTEGER | Number of hot chain data blocks |
| NUM_AUXILLIARY | INTEGER | Number of auxiliary chain data blocks |
| NUM_WRITE | INTEGER | Number of dirty page chain data blocks |
| NUM_TEMP | INTEGER | Number of temporary chain data blocks |
| NAME | VARCHAR(32) | The name of the cache, currently fixed as DEFAULT |
| SET_MSIZE | BIGINT | The maximum number of data blocks that can be set in the cache |
| BLOCK_SIZE | BIGINT | The size of the data blocks managed in the cache pool |
| PHYSICAL_READS | BIGINT | Number of physical reads |
| PHYSICAL_WRITES | BIGINT | Number of physical writes |