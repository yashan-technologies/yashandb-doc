This view shows the statistics of VMs for all nodes in an ISC distributed cluster. 

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | INTEGER  | Group ID                     |
| GROUP_NODE_ID| INTEGER  | Node ID within the group     |
| SID          | INTEGER  | Current session ID           |
| ALLOC_COUNT  | BIGINT   | Total allocation count        |
| OPEN_COUNT   | BIGINT   | Total open count             |
| CLOSE_COUNT  | BIGINT   | Total close count            |
| FREE_COUNT   | BIGINT   | Total free count             |
| SWAP_OUT_COUNT| BIGINT  | Total swap-out to disk count |
| SWAP_IN_COUNT| BIGINT   | Total swap-in to memory count |
| IO_WAIT_COUNT| BIGINT   | Total IO wait count          |
| EXTEND_COUNT | BIGINT   | Total VM page extension count |
| CURR_OPEN    | INTEGER  | Current number of pages open  |
| CURR_CLOSE   | INTEGER  | Current number of records closed |
| CURR_SWAP_OUT | INTEGER | Current number of pages swapping out to disk |