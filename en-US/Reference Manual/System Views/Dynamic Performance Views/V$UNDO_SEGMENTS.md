This view is used to display the usage of undo blocks in all current undo segments in the database.

|Field |Type |Description |
| --- | --- | --- |
| ID  | BIGINT | segment ID |
| USED_TIME      | DATE     | The time when the transaction that first committed the block returned to the undo segment.                     |
| SURPLUS_COUNT  | INTEGER  | The number of surplus blocks while maintaining automatic balancing optimization of the undo chain.             |
| UBLK_COUNT     | INTEGER  | The number of undo blocks returned by committed transactions.                                                 |
| FIRST_UBAFIL   | INTEGER  | The file number of the first undo block returned by committed transactions.                                   |
| FIRST_UBABLK   | INTEGER  | The ID of the first undo block returned by committed transactions.                                            |
| LAST_UBAFIL    | INTEGER  | The file number of the last undo block returned by committed transactions.                                    |
| LAST_UBABLK    | INTEGER  | The ID of the last undo block returned by committed transactions.                                             |
| UFB_COUNT      | INTEGER  | The number of uninitialized undo blocks.                                                                      |
| FIRST_UFBFIL   | INTEGER  | The file number of the first uninitialized undo block.                                                       |
| FIRST_UFBBLK   | INTEGER  | The ID of the first uninitialized undo block.                                                                |
| XBLKS          | INTEGER  | The number of transaction blocks managed by the undo segment (reserved field).                                |
| FREE\_COUNT | INTEGER | The number of undo blocks which can be reused immediate after returned by committed transactions |