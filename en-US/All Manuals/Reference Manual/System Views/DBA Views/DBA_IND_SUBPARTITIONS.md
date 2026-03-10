This view displays information about each secondary partition of all composite partition indexes.

|Field |Type |Description |
| --------------------------- | ------------- | ------------------------------------------------ |
| INDEX_OWNER                | VARCHAR(64)   | Username of the index                             |
| INDEX_NAME                 | VARCHAR(64)   | Index name                                       |
| PARTITION_NAME             | VARCHAR(64)   | Name of the primary partition                     |
| SUBPARTITION_NAME          | VARCHAR(64)   | Name of the secondary partition                   |
| HIGH_VALUE                 | VARCHAR(4000) | Boundary value string of the secondary partition   |
| HIGH_VALUE_LENGTH         | INTEGER       | Length of the boundary value string of the secondary partition |
| PARTITION_POSITION         | BIGINT        | Position of the partition within the index        |
| SUBPARTITION_POSITION      | BIGINT        | Position of the secondary partition within the primary partition |
| STATUS                      | VARCHAR(8)    | Availability of the partition<br>\*   USABLE<br>\*   UNUSABLE |
| TABLESPACE_NAME            | VARCHAR(64)   | Name of the tablespace where the secondary partition is located |
| PCT_FREE                   | INTEGER       | Percent of space reserved in data blocks         |
| INI_TRANS                  | INTEGER       | Number of Xslots preset during page initialization |
| MAX_TRANS                  | INTEGER       | Maximum number of Xslots the data page can expand to |
| LOGGING                     | CHAR(1)       | Whether the partition logs redo<br>\*   Y<br>\*   N         |
| BLEVEL                      | INTEGER       | Number of levels in the BTree                    |
| LEAF_BLOCKS                | BIGINT        | Number of leaf nodes in the BTree                |
| DISTINCT_KEYS              | BIGINT        | Number of distinct key values                     |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT        | Average number of leaf blocks per key            |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT        | Average number of blocks per key                  |
| NUM_ROWS                   | BIGINT        | Number of rows in the index                       |
| SAMPLE_SIZE                | BIGINT        | Number of sample rows for index partition statistics |
| LAST_ANALYZED              | DATE          | Time of the last statistics collection             |
| INTERVAL$                   | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N             |
| SEGMENT_CREATED            | VARCHAR(1)    | Whether the segment is created<br>\*   Y<br>\*   N              |