This view displays the statistics content of the index on the dictionary cache.

|Field |Type |Description |
| --- | --- | --- |
| OBJ# | BIGINT | Index object ID |
| PART# | BIGINT | Index partition ID. If the index object has no partition, this field is NULL |
| FLAGS | INTEGER | Index properties<br>\*   0X01: Whether partitioned <br>\*   0X02: Whether it is usable<br>\*   0X4: Whether it is a function index<br>\*   0X8: Whether it is reversed<br>\*   0X10: Whether it is used as a constraint<br>\*   0X20: Whether it is visible<br>\*   0X40: Whether DATAOID is recorded on the KEY<br>\*   0X80: Indicates whether the statistics are directly specified by the user<br>\*   0X100: Indicates whether the partition index statistics are obtained through full statistics or estimated from partitions |
| ROW_COUNT | BIGINT | Number of rows in the index |
| LEAFBLOCK_COUNT | BIGINT | Number of Leaf Blocks |
| DISTINCT_KEYS | BIGINT | Number of unique key values |
| DISTINCT_FKEYS | BIGINT | Number of unique values in the first column of the composite index |
| BTREE_LEVEL | INTEGER | Number of levels in the BTree |
| CLUSTER_FACTOR | BIGINT | Cluster factor |
| AVG_KEY_LBLOCKS | BIGINT | Average number of leaf blocks occupied per key value |
| AVG_KEY_DBLOCKS | BIGINT | Average number of data blocks occupied per key value |
| ANALYZE_TIME | DATE | Time of the last statistics collection |
| SAMPLESIZE | BIGINT | Number of rows sampled for statistics |
| DISTINCT_2KEYS | BIGINT | Number of unique values in the first two columns of the composite index |
| DISTINCT_3KEYS | BIGINT | Number of unique values in the first three columns of the composite index |
| DISTINCT_4KEYS | BIGINT | Number of unique values in the first four columns of the composite index |