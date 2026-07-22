This view shows the statistics of the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | The identifier of the buffer partition |
| SIZE | BIGINT | The size of the buffer partition (in bytes) |
| NUM\_TOTAL | INTEGER | Total number of data blocks |
| NUM\_RESIDENT | INTEGER | Number of resident memory data blocks |
| NUM\_MAIN | INTEGER | Number of hot chain data blocks |
| NUM\_AUXILLIARY | INTEGER | Number of auxiliary chain data blocks |
| NUM\_WRITE | INTEGER | Number of dirty page chain data blocks |
| NUM\_TEMP | INTEGER | Number of temporary chain data blocks |
| NAME | VARCHAR(32) | The name of the cache, currently fixed as DEFAULT |
| SET\_MSIZE | BIGINT | The maximum number of data blocks that can be set in the cache |
| BLOCK\_SIZE | BIGINT | The size of the data blocks managed in the cache pool |
| PHYSICAL\_READS | BIGINT | Number of physical reads |
| PHYSICAL\_WRITES | BIGINT | Number of physical writes |