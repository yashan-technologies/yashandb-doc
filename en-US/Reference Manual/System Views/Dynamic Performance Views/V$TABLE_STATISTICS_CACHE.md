This view displays the statistics content of the dictionary cache table.

|Field |Type |Description |
| --- | --- | --- |
| OBJ#       | BIGINT   | Table object ID                                                                                                                                                  |
| PART#      | BIGINT   | Table partition ID; if the table object does not have a partition, this field will be NULL                                                                       |
| FLAGS      | INTEGER  | Table identification<br>\*   0X01: Whether it is a sharded table <br>\*   0X02: Whether it is a duplicated table <br>\*   0X4: Whether statistics are specified by the user <br>\*   0X8: Indicates whether the statistics of the partition table are obtained from full statistics or estimated from partitions |
| ROW_COUNT  | BIGINT   | Number of rows in the table                                                                                                                                     |
| BLOCK_COUNT| BIGINT   | Quantity of data blocks in the table                                                                                                                             |
| EMPTY_COUNT| BIGINT   | Number of empty data blocks                                                                                                                                     |
| CHAIN_COUNT| BIGINT   | Number of row links                                                                                                                                             |
| AVG_ROW_SIZE| INTEGER | Average row length                                                                                                                                              |
| AVG_SPACE  | INTEGER  | Average space utilization rate                                                                                                                                  |
| ANALYZE_TIME| DATE    | Last time statistics were collected                                                                                                                              |
| SAMPLESIZE | BIGINT   | Number of rows sampled for statistics                                                                                                                            |