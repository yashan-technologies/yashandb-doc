This view displays the statistics of the VM.

|Field |Type |Description |
| --- | --- | --- |
| SID            | INTEGER  | The ID of the current session       |
| ALLOC_COUNT    | BIGINT   | The total number of allocations     |
| OPEN_COUNT     | BIGINT   | The total number of opens           |
| CLOSE_COUNT    | BIGINT   | The total number of closes          |
| FREE_COUNT     | BIGINT   | The total number of frees           |
| SWAP_OUT_COUNT | BIGINT   | The total number of swaps out to disk |
| SWAP_IN_COUNT  | BIGINT   | The total number of swaps in to memory |
| IO_WAIT_COUNT  | BIGINT   | The total number of IO wait occurrences |
| EXTEND_COUNT   | BIGINT   | The total number of VM page extensions |
| CURR_OPEN      | INTEGER  | The current number of pages being opened |
| CURR_CLOSE     | INTEGER  | The current number of closed records |
| CURR_SWAP_OUT  | INTEGER  | The current number of pages being swapped out to disk |