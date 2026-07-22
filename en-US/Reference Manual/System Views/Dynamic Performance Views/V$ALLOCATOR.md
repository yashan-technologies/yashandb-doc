This view shows the current memory usage status.

|Field |Type |Description |
| --- | --- | --- |
| NAME              | VARCHAR(64) | Memory type                                           |
| TOTAL_MEMORY      | BIGINT      | Total size of allocated memory (in bytes)            |
| CURR_MEMORY_USED  | BIGINT      | Total size of currently used memory (in bytes)       |
| FREE_MEMORY       | BIGINT      | Current remaining memory size (in bytes)             |
| MAX_MEMORY_USED    | BIGINT      | Peak memory use during the current runtime of the node (in bytes) |