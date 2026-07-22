This view is used to detect VM usage.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | NUMBER    | Group ID                                                                                         |
| GROUP_NODE_ID     | NUMBER    | Node ID within the group                                                                         |
| INST_ID           | NUMBER    | Instance ID                                                                                      |
| ID                | SMALLINT  | Partition ID                                                                                    |
| TOTAL_BLOCKS      | INTEGER   | Total number of memory pages                                                                     |
| FREE_BLOCKS       | INTEGER   | Number of memory pages that are in a free state                                                  |
| OPENED_BLOCKS     | INTEGER   | Number of memory pages that are in an opened state                                               |
| CLOSED_BLOCKS     | INTEGER   | Number of memory pages that are in a closed state                                                |
| SWAPPED_OUT_BLOCKS| INTEGER   | Number of memory pages that have been swapped out to disk                                         |
| CTRL_BLOCKS       | INTEGER   | Number of control pages                                                                           |
| FREE_SWAP_BLOCKS  | INTEGER   | Total number of free pages available for swapping out. When there are different partitions, the total number of free pages available for swapping out is displayed for each partition. |