This view displays a summary of the undo segment information for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ID  | BIGINT | segment ID |
| USED_TIME | DATE | The time of the transaction that submitted the first undo block in the blocks returned by committed transactions on the undo segment |
| SURPLUS_COUNT | INTEGER | The number of excess blocks while maintaining automatic balancing optimization of the undo chain |
| UBLK_COUNT | INTEGER | The number of undo blocks returned by committed transactions |
| FIRST_UBAFIL | INTEGER | The file number of the first undo block returned by committed transactions |
| FIRST_UBABLK | INTEGER | The ID of the first undo block returned by committed transactions |
| LAST_UBAFIL | INTEGER | The file number of the last undo block returned by committed transactions |
| LAST_UBABLK | INTEGER | The ID of the last undo block returned by committed transactions |
| UFB_COUNT | INTEGER | The number of uninitialized undo blocks |
| FIRST_UFBFIL | INTEGER | The file number of the first uninitialized undo block |
| FIRST_UFBBLK | INTEGER | The ID of the first uninitialized undo block |
| XBLKS | INTEGER | The number of transaction blocks managed by the undo segment |