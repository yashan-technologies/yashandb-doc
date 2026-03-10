This view is used to display the usage of undo blocks in all current undo segments in the database.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER  | Group ID                                                                           |
| GROUP_NODE_ID  | NUMBER  | Node ID within the group                                                           |
| INST_ID        | NUMBER  | Instance ID                                                                        |
| ID  | BIGINT | segment ID |
| USED_TIME      | DATE    | The time of the transaction that first committed where the returned blocks are located in the undo segment |
| SURPLUS_COUNT   | INTEGER | The surplus number of blocks under the condition of maintaining automatic balancing optimization of the undo chain |
| UBLK_COUNT     | INTEGER | The number of undo blocks returned by committed transactions                       |
| FIRST_UBAFIL   | INTEGER | The file number of the first undo block returned by a committed transaction         |
| FIRST_UBABLK   | INTEGER | The ID of the first undo block returned by a committed transaction                 |
| LAST_UBAFIL    | INTEGER | The file number of the last undo block returned by a committed transaction          |
| LAST_UBABLK    | INTEGER | The ID of the last undo block returned by a committed transaction                   |
| UFB_COUNT      | INTEGER | The number of uninitialized undo blocks                                            |
| FIRST_UFBFIL   | INTEGER | The file number of the first uninitialized undo block                               |
| FIRST_UFBBLK   | INTEGER | The ID of the first uninitialized undo block                                       |
| XBLKS          | INTEGER | The number of transaction blocks managed by the undo segment (reserved field)      |