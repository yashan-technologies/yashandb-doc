This view shows the statistics of the Database Writer thread.

|Field |Type |Description |
| --- | --- | --- |
| ID               | INTEGER   | ID of the Database Writer                                                                              |
| CURRENT_STATUS   | VARCHAR(32) | Current status of the thread<br>\* CHECKPOINT: Checkpoint triggers flush<br>\* BUFFER CLEAN: Dirty page elimination triggers flush<br>\* CURRENT CLEAN: Pastcopy triggers flush<br>\* OBJECT CLEAN: Drop object triggers flush<br>\* IDLE: Idle  |
| CAPACITY         | INTEGER   | Maximum block capacity of the Database Writer                                                          |
| BLOCK_NUM        | INTEGER   | Number of blocks being flushed currently                                                                |
| BUFFER_CLEAN_NUM | BIGINT    | Number of times buffer clean has been executed                                                         |
| CHECKPOINT_NUM   | BIGINT    | Number of times checkpoint has been executed                                                           |
| PINNED_BLOCKS    | BIGINT    | Number of pinned blocks encountered while preparing blocks                                             |
| CURRENT_CLEAN_NUM | BIGINT   | Number of times current clean has been executed                                                         |
| OBJECT_CHECKPOINT_NUM | BIGINT | Number of times object checkpoint has been executed                                                     |