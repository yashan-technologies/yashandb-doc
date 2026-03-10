This view is used to detect VM usage.

|Field |Type |Description |
| --- | --- | --- |
| ID  | SMALLINT | Partition ID |
| TOTAL_BLOCKS | INTEGER | Total number of memory pages |
| FREE_BLOCKS | INTEGER | Number of free memory pages |
| OPENED_BLOCKS | INTEGER | Number of opened memory pages |
| CLOSED_BLOCKS | INTEGER | Number of closed memory pages |
| SWAPPED_OUT_BLOCKS | INTEGER | Number of memory pages swapped out to disk |
| CTRL_BLOCKS | INTEGER | Number of control pages |
| FREE_SWAP_BLOCKS | INTEGER | Total number of free pages available for swapping; when there are different partitions, each partition shows the total number of free pages available for swapping across all partitions |