This view displays the statistics of all indexes.

|Field |Type |Description |
| --- | --- | --- |
| OWNER                  | VARCHAR(64) | Username of the owner of the index                   |
| INDEX_NAME             | VARCHAR(64) | Name of the index                                    |
| TABLE_OWNER            | VARCHAR(64) | Username of the owner of the indexed table           |
| TABLE_NAME             | VARCHAR(64) | Name of the indexed table                            |
| PARTITION_NAME         | VARCHAR(64) | Name of the partition                                 |
| PARTITION_POSITION      | BIGINT      | Partition number                                     |
| SUBPARTITION_NAME      | VARCHAR(1)  | Name of the subpartition                             |
| SUBPARTITION_POSITION   | VARCHAR(1)  | Subpartition number                                  |
| OBJECT_TYPE            | VARCHAR(9)  | Type of object                                       |
| BLEVEL                 | INTEGER     | Height of the BTree                                  |
| LEAF_BLOCKS           | BIGINT      | Number of leaf blocks                                |
| DISTINCT_KEYS          | BIGINT      | Number of unique values                              |
| DISTINCT_FKEYS        | BIGINT      | Number of unique values in the first column of the composite index |
| DISTINCT_2KEYS        | BIGINT      | Number of unique values in the first two columns of the composite index |
| DISTINCT_3KEYS        | BIGINT      | Number of unique values in the first three columns of the composite index |
| DISTINCT_4KEYS        | BIGINT      | Number of unique values in the first four columns of the composite index |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT     | Average number of keys per leaf block                |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT     | Average number of keys per block                     |
| CLUSTERING_FACTOR      | BIGINT      | Clustering factor of the index                       |
| NUM_ROWS               | BIGINT      | Number of index keys                                 |
| SAMPLE_SIZE            | BIGINT      | Number of sampled rows                                |
| LAST_ANALYZED         | DATE        | Time of the last collection                           |
| GLOBAL_STATS           | VARCHAR(1)  | Whether the statistics are global                    |
| USER_STATS             | VARCHAR(1)  | Whether the statistics are user-defined              |
| LOCKED_STATS           | VARCHAR(1)  | Whether the statistics are locked<br/>* Y: Locked<br/>* N: Not locked<br/>* Empty: Statistics not collected |
| STALE_STATS            | CHAR(1)     | Whether the statistics are stale, in the current version YAC Deployment mode, the stale judgment is based only on the modifications of the current instance<br/>* Y: Stale<br/>* N: Not stale<br/>* Empty: Statistics not collected |
| SCOPE                  | CHAR(6)     | Scope of statistics for temporary table indexes      |