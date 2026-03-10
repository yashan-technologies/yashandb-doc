This view shows the summary information of hot page buffers for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER  | Group ID                                                           |
| GROUP_NODE_ID     | INTEGER  | Node ID within the group                                           |
| FREE_COUNT        | INTEGER  | Current number of available free hot page buffer pages             |
| GENERATE_COUNT    | BIGINT   | Number of hot page buffer pages allocated since the database started |
| RECYCLE_COUNT     | BIGINT   | Number of hot page buffer pages reclaimed since the database started  |
| HWM                | INTEGER  | Current high water mark of hot page buffers in the database (hot page buffers beyond this high water mark have not been allocated) |
| TOTAL_COUNT       | INTEGER  | Total number of hot page buffer pages in the database              |
| BLOCK_SIZE        | INTEGER  | Size of a single hot page buffer page (unit: bytes)               |