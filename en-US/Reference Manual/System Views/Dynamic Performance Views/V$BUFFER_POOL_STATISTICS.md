This view displays the statistics of the data buffer.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | The buffer number |
| SIZE | BIGINT | The size of the buffer (unit: bytes) |
| NUM\_TOTAL | INTEGER | Total number of data blocks |
| NUM\_RESIDENT | INTEGER | Number of resident memory data blocks |
| NUM\_MAIN | INTEGER | Number of hot chain data blocks |
| NUM\_AUXILLIARY | INTEGER | Number of auxiliary chain data blocks |
| NUM\_WRITE | INTEGER | Number of dirty page chain data blocks |
| NUM\_TEMP | INTEGER | Number of temporary chain data blocks |
| NAME | VARCHAR(32) | Name of the cache, currently fixed as DEFAULT |
| SET\_MSIZE | BIGINT | Maximum number of data blocks that can be accommodated in the cache |
| BLOCK\_SIZE | BIGINT | Size of data blocks managed in the cache pool |
| DB\_BLOCK\_GETS | BIGINT | Number of times the latest version of data blocks is accessed from the cache |
| CONSISTENT\_GETS | BIGINT | Number of consistent reads |
| PHYSICAL\_READS | BIGINT | Number of physical reads |
| PHYSICAL\_WRITES | BIGINT | Number of physical writes |