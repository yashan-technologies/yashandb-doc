This view shows the overall memory information of VMs for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID       | INTEGER   | Group ID                                                                                      |
| GROUP\_NODE\_ID | INTEGER   | Node ID within the group                                                                      |
| ID              | SMALLINT  | Partition ID                                                                                  |
| TOTAL\_BLOCKS   | INTEGER   | Total number of memory pages                                                                   |
| FREE\_BLOCKS    | INTEGER   | Number of memory pages that are in a free state                                               |
| OPENED\_BLOCKS  | INTEGER   | Number of memory pages that are in an opened state                                            |
| CLOSED\_BLOCKS  | INTEGER   | Number of memory pages that are in a closed state                                            |
| SWAPPED\_OUT\_BLOCKS | INTEGER | Number of memory pages that have been swapped out to disk                                      |
| CTRL\_BLOCKS    | INTEGER   | Number of control pages                                                                        |
| FREE\_SWAP\_BLOCKS | INTEGER | Total number of free pages available for swapping; when different partitions exist, each partition shows the total number of free pages available for swapping across all partitions |