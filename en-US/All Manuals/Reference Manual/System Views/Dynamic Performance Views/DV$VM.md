This view shows the overall memory information of VMs for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER   | Group ID                                                                                      |
| GROUP_NODE_ID | INTEGER   | Node ID within the group                                                                      |
| ID              | SMALLINT  | Partition ID                                                                                  |
| TOTAL_BLOCKS   | INTEGER   | Total number of memory pages                                                                   |
| FREE_BLOCKS    | INTEGER   | Number of memory pages that are in a free state                                               |
| OPENED_BLOCKS  | INTEGER   | Number of memory pages that are in an opened state                                            |
| CLOSED_BLOCKS  | INTEGER   | Number of memory pages that are in a closed state                                            |
| SWAPPED_OUT_BLOCKS | INTEGER | Number of memory pages that have been swapped out to disk                                      |
| CTRL_BLOCKS    | INTEGER   | Number of control pages                                                                        |
| FREE_SWAP_BLOCKS | INTEGER | Total number of free pages available for swapping; when different partitions exist, each partition shows the total number of free pages available for swapping across all partitions |