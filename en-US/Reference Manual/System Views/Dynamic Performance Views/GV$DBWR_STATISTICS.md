This view displays the statistics of the Database Writer thread.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER  | Group ID                                                                                      |
| GROUP_NODE_ID    | NUMBER  | Node ID within the group                                                                      |
| INST_ID          | NUMBER  | Instance ID                                                                                    |
| ID               | INTEGER | Database Writer ID                                                                             |
| CURRENT_STATUS   | VARCHAR(32) | Current status of the thread<br>\* CHECKPOINT: Checkpoint triggers flushing<br>\* BUFFER CLEAN: Dirty page eviction triggers flushing<br>\* CURRENT CLEAN: Pastcopy triggers flushing<br>\* OBJECT CLEAN: Drop object triggers flushing<br>\* IDLE: Idle  |
| CAPACITY         | INTEGER | Maximum block capacity of Database Writer                                                       |
| BLOCK_NUM        | INTEGER | Number of blocks currently being flushed                                                       |
| BUFFER_CLEAN_NUM | BIGINT  | Number of times buffer clean has been executed                                                  |
| CHECKPOINT_NUM   | BIGINT  | Number of times checkpoint has been executed                                                   |
| PINNED_BLOCKS    | BIGINT  | Number of pinned blocks encountered when preparing blocks                                      |
| CURRENT_CLEAN_NUM| BIGINT  | Number of times current clean has been executed                                                |
| OBJECT_CHECKPOINT_NUM | BIGINT | Number of times object checkpoint has been executed                                            |