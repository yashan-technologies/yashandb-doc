This view displays the statistics of the index on the dictionary buffer.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | NUMBER  | Group ID                                                                                       |
| GROUP_NODE_ID        | NUMBER  | Node ID within the group                                                                        |
| INST_ID              | NUMBER  | Instance ID                                                                                    |
| OBJ#                 | BIGINT  | Index object ID                                                                                 |
| PART#                | BIGINT  | Index partition ID; if the index object has no partition, this field will be NULL             |
| FLAGS                | INTEGER | Properties of the index<br>\*   0X01: Whether it is partitioned <br>\*   0X02: Whether it is usable<br>\*   0X4: Whether it is a function index<br>\*   0X8: Whether it is in reverse order<br>\*   0X10: Whether it is used as a constraint<br>\*   0X20: Whether it is visible<br>\*   0X40: Whether DATAOID is recorded on the KEY<br>\*   0X80: Indicates whether the statistics are directly specified by the user<br>\*   0X100: Indicates whether the statistics of the partition index are obtained from full statistics or estimated from partitions |
| ROW_COUNT            | BIGINT  | Number of rows in the index                                                                     |
| LEAFBLOCK_COUNT      | BIGINT  | Number of Leaf Blocks                                                                            |
| DISTINCT_KEYS        | BIGINT  | Number of unique key values                                                                      |
| DISTINCT_FKEYS      | BIGINT  | Number of unique values in the first column of the composite index                             |
| BTREE_LEVEL          | INTEGER | Number of levels in the BTree                                                                    |
| CLUSTER_FACTOR       | BIGINT  | Clustering factor                                                                                |
| AVG_KEY_LBLOCKS      | BIGINT  | Average number of leaf blocks occupied by each key value                                         |
| AVG_KEY_DBLOCKS      | BIGINT  | Average number of data blocks occupied by each key value                                         |
| ANALYZE_TIME         | DATE    | Last time statistics were collected                                                              |
| SAMPLESIZE           | BIGINT  | Number of rows sampled for statistics                                                            |
| DISTINCT_2KEYS      | BIGINT  | Number of unique values in the first two columns of the composite index                        |
| DISTINCT_3KEYS      | BIGINT  | Number of unique values in the first three columns of the composite index                      |
| DISTINCT_4KEYS      | BIGINT  | Number of unique values in the first four columns of the composite index                       |