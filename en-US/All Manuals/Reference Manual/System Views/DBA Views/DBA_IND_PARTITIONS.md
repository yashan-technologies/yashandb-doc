This view displays information about each partition of all partition indexes.

|Field |Type |Description |
| --- | --- | --- |
| INDEX_OWNER          | VARCHAR(64)   | The username of the index                                  |
| INDEX_NAME           | VARCHAR(64)   | The name of the index                                      |
| COMPOSITE             | VARCHAR(1)    | Whether it is a composite partition index<br>\*   Y<br>\*   N |
| PARTITION_NAME       | VARCHAR(64)   | The name of the index partition                            |
| SUBPARTITION_COUNT   | BIGINT        | The number of subpartitions corresponding to the index partition |
| HIGH_VALUE           | VARCHAR(4000) | The boundary value string of the index partition           |
| HIGH_VALUE_LENGTH   | INTEGER       | The length of the boundary value string of the index partition |
| PARTITION_POSITION    | BIGINT        | The position of the index partition within the index       |
| STATUS                | VARCHAR(8)    | Whether the index partition is usable<br>\*   USABLE<br>\*   UNUSABLE<br>*   N/A: For secondary partition indexes, check the status in DBA_IND_SUBPARTITIONS |
| TABLESPACE_NAME      | VARCHAR(64)   | The name of the tablespace where the index partition is located |
| PCT_FREE             | INTEGER       | The percentage of space to be reserved at least within the data block |
| INI_TRANS            | INTEGER       | The preset number of Xslots during the initialization of the data block |
| MAX_TRANS            | INTEGER       | The maximum number of Xslots the data block can expand to  |
| LOGGING               | CHAR(1)      | Whether the index partition records redo<br>\*   Y<br>\*   N |
| BLEVEL                | INTEGER       | The number of layers in the BTree                          |
| LEAF_BLOCKS          | BIGINT        | The number of leaf nodes in the BTree                       |
| DISTINCT_KEYS        | BIGINT        | The number of unique key values                             |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT | The average number of leaf blocks occupied by each key value |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT | The average number of blocks occupied by each key value     |
| NUM_ROWS             | BIGINT        | The number of rows in the index                             |
| SAMPLE_SIZE          | BIGINT        | The number of sampled rows for index partition statistics    |
| LAST_ANALYZED       | DATE          | The time of the last execution of statistics collection      |
| INTERVAL$             | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N     |
| SEGMENT_CREATED      | VARCHAR(3)    | Whether the segment has been created<br>\*   Y<br>\*   N<br>*   N/A |