This view displays VM statistics.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER  | Group ID                     |
| GROUP_NODE_ID  | NUMBER  | Node ID within the group     |
| INST_ID        | NUMBER  | Instance ID                  |
| SID            | INTEGER | Current session ID           |
| ALLOC_COUNT    | BIGINT  | Total number of allocations   |
| OPEN_COUNT     | BIGINT  | Total number of opens        |
| CLOSE_COUNT    | BIGINT  | Total number of closes       |
| FREE_COUNT     | BIGINT  | Total number of frees        |
| SWAP_OUT_COUNT | BIGINT  | Total number of swaps out to disk |
| SWAP_IN_COUNT  | BIGINT  | Total number of swaps in to memory |
| IO_WAIT_COUNT  | BIGINT  | Total number of IO waits     |
| EXTEND_COUNT   | BIGINT  | Total number of VM page extensions |
| CURR_OPEN      | INTEGER | Number of pages currently opening |
| CURR_CLOSE     | INTEGER | Number of records currently closed |
| CURR_SWAP_OUT  | INTEGER | Number of pages currently swapping out to disk |