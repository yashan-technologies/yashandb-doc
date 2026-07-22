This view displays a summary of the undo segment information for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| ID  | BIGINT | segment ID |
| USED\_TIME | DATE | The time of the transaction that submitted the first undo block in the blocks returned by committed transactions on the undo segment |
| SURPLUS\_COUNT | INTEGER | The number of excess blocks while maintaining automatic balancing optimization of the undo chain |
| UBLK\_COUNT | INTEGER | The number of undo blocks returned by committed transactions |
| FIRST\_UBAFIL | INTEGER | The file number of the first undo block returned by committed transactions |
| FIRST\_UBABLK | INTEGER | The ID of the first undo block returned by committed transactions |
| LAST\_UBAFIL | INTEGER | The file number of the last undo block returned by committed transactions |
| LAST\_UBABLK | INTEGER | The ID of the last undo block returned by committed transactions |
| UFB\_COUNT | INTEGER | The number of uninitialized undo blocks |
| FIRST\_UFBFIL | INTEGER | The file number of the first uninitialized undo block |
| FIRST\_UFBBLK | INTEGER | The ID of the first uninitialized undo block |
| XBLKS | INTEGER | The number of transaction blocks managed by the undo segment |
| FREE\_COUNT | INTEGER | The number of undo blocks which can be reused immediate after returned by committed transactions |
| IS\_TEMP | BOOLEAN | Whether to use an undo segment for the temporary tablespace |