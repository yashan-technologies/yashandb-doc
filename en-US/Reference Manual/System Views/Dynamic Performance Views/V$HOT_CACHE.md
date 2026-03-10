This view is used to check the current usage of hot page buffers in the database.

|Field |Type |Description |
| --- | --- | --- |
| FREE_COUNT    | INTEGER | The number of currently available free hot page buffer pages     |
| GENERATE_COUNT | BIGINT  | The number of hot page buffer pages allocated since the database started |
| RECYCLE_COUNT  | BIGINT  | The number of hot page buffer pages recycled since the database started |
| HWM            | INTEGER | The high watermark of the current database BTree buffer pages (hot page buffer pages beyond this high watermark have not been allocated) |
| TOTAL_COUNT    | INTEGER | The total number of hot page buffer pages in the database       |
| BLOCK_SIZE     | INTEGER | The size of a single hot page buffer page (unit: bytes)        |