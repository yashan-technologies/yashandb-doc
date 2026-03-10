This view is used to check the current usage of the Hot Cache in the database.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                                |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                                |
| INST_ID       | NUMBER  | Instance ID                                            |
| FREE_COUNT    | INTEGER | The number of currently available free hot page buffer pages |
| GENERATE_COUNT| BIGINT  | The number of hot page buffer pages allocated since the database started |
| RECYCLE_COUNT | BIGINT  | The number of hot page buffer pages recycled since the database started |
| HWM           | INTEGER | The high water mark of the current database BTree buffer pages (hot page buffer pages beyond the high water mark have not been allocated yet) |
| TOTAL_COUNT   | INTEGER | The total number of hot page buffer pages in the database |
| BLOCK_SIZE    | INTEGER | The size of a single hot page buffer page (unit: bytes) |