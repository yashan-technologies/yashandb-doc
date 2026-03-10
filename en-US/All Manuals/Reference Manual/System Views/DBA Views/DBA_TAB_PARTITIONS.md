This view displays the partition information of all partitioned tables.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_OWNER         | VARCHAR(64)   | Username of the table                             |
| TABLE_NAME         | VARCHAR(64)   | Name of the table to which the partition belongs  |
| COMPOSITE            | VARCHAR(1)    | Whether it is a composite partition               |
| PARTITION_NAME      | VARCHAR(64)   | Name of the partition                             |
| SUBPARTITION_COUNT  | BIGINT        | Number of subpartitions in the current partition  |
| HIGH_VALUE          | VARCHAR(4000) | String of the partition boundary value            |
| HIGH_VALUE_LENGTH  | INTEGER       | Length of the partition boundary value string (in bytes) |
| PARTITION_POSITION   | BIGINT        | Position of the partition within the table       |
| TABLESPACE_NAME     | VARCHAR(64)   | Name of the tablespace where the partition is located |
| PCT_FREE            | INTEGER       | Percentage of space reserved within data pages    |
| INI_TRANS           | INTEGER       | Number of Xslots reserved when initializing data pages |
| MAX_TRANS           | INTEGER       | Maximum number of Xslots that can be extended in data pages |
| LOGGING              | CHAR(1)      | Whether the partition is logging redo<br>\*   Y<br>\*   N |
| NUM_ROWS            | BIGINT        | Number of rows in the partition                   |
| BLOCKS               | BIGINT        | Number of blocks in the partition                 |
| EMPTY_BLOCKS        | BIGINT        | Number of empty blocks in the partition           |
| AVG_SPACE           | INTEGER       | Average free space in partition blocks (in bytes) |
| CHAIN_CNT           | BIGINT        | Number of row links in the partition              |
| AVG_ROW_LEN       | INTEGER       | Average row length in the partition (in bytes)   |
| SAMPLE_SIZE         | BIGINT        | Number of sampled rows for statistics             |
| LAST_ANALYZED      | DATE          | Last time statistics were collected               |
| INTERVAL$            | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N |
| SEGMENT_CREATED     | VARCHAR(1)    | Whether the segment is created<br>\*   Y<br>\*   N |