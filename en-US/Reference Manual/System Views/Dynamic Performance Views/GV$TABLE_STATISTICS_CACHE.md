This view displays the statistics content of the dictionary cache table.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                                                                |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                                                                |
| INST_ID       | NUMBER  | Instance ID                                                                             |
| OBJ#          | BIGINT  | Table object ID                                                                         |
| PART#         | BIGINT  | Table partition ID; if the table object has no partition, this field is NULL           |
| FLAGS         | INTEGER | Table identifier<br>\*   0X01: indicates whether it is a sharded table <br>\*   0X02: indicates whether it is a duplicated table <br>\*   0X4: indicates whether statistics are directly specified by the user<br>\*   0X8: indicates whether the statistics of the partition table are obtained from complete statistics or estimated from the partition |
| ROW_COUNT     | BIGINT  | Number of rows in the table                                                            |
| BLOCK_COUNT   | BIGINT  | Number of data blocks in the table                                                      |
| EMPTY_COUNT   | BIGINT  | Number of empty data blocks                                                             |
| CHAIN_COUNT   | BIGINT  | Number of row links                                                                     |
| AVG_ROW_SIZE  | INTEGER | Average row length                                                                      |
| AVG_SPACE     | INTEGER | Average space usage rate                                                                 |
| ANALYZE_TIME  | DATE    | The last time statistics were collected                                                  |
| SAMPLESIZE    | BIGINT  | Number of rows sampled for statistics                                                    |