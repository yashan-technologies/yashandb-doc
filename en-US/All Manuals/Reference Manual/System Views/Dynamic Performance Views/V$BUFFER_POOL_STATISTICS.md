This view displays the statistics of the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | The buffer number |
| SIZE | BIGINT | The size of the buffer (unit: bytes) |
| NUM_TOTAL | INTEGER | Total number of data blocks |
| NUM_RESIDENT | INTEGER | Number of resident memory data blocks |
| NUM_MAIN | INTEGER | Number of hot chain data blocks |
| NUM_AUXILLIARY | INTEGER | Number of auxiliary chain data blocks |
| NUM_WRITE | INTEGER | Number of dirty page chain data blocks |
| NUM_TEMP | INTEGER | Number of temporary chain data blocks |
| NAME | VARCHAR(32) | Name of the cache, currently fixed as DEFAULT |
| SET_MSIZE | BIGINT | Maximum number of data blocks that can be accommodated in the cache |
| BLOCK_SIZE | BIGINT | Size of data blocks managed in the cache pool |
| DB_BLOCK_GETS | BIGINT | Number of times the latest version of data blocks is accessed from the cache |
| CONSISTENT_GETS | BIGINT | Number of consistent reads |
| PHYSICAL_READS | BIGINT | Number of physical reads |
| PHYSICAL_WRITES | BIGINT | Number of physical writes |