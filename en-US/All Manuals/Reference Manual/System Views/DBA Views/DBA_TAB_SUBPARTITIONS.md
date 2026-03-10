This view displays all subpartition information.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_OWNER         | VARCHAR(64)  | The username of the table            |
| TABLE_NAME          | VARCHAR(64)  | The name of the table to which the partition belongs |
| PARTITION_NAME      | VARCHAR(64)  | The name of the partition            |
| SUBPARTITION_NAME   | VARCHAR(64)  | The name of the subpartition         |
| HIGH_VALUE          | VARCHAR(4000)| The boundary value string of the subpartition |
| HIGH_VALUE_LENGTH  | INTEGER      | The length of the subpartition boundary value string (in bytes) |
| PARTITION_POSITION   | BIGINT       | The position of the partition within the table |
| SUBPARTITION_POSITION| BIGINT       | The position of the subpartition within the table |
| TABLESPACE_NAME     | VARCHAR(64)  | The tablespace name where the subpartition is located |
| PCT_FREE            | INTEGER      | The percentage of space reserved in data pages |
| INI_TRANS           | INTEGER      | The number of Xslots reserved when initializing the data page |
| MAX_TRANS           | INTEGER      | The maximum number of Xslots to which the data page can expand |
| LOGGING              | CHAR(1)     | Whether the partition records redo<br>\*   Y<br>\*   N |
| NUM_ROWS            | BIGINT       | The number of rows in the subpartition |
| BLOCKS               | BIGINT       | The number of blocks in the subpartition |
| EMPTY_BLOCKS        | BIGINT       | The number of empty blocks in the subpartition |
| AVG_SPACE           | INTEGER      | The average free space in subpartition blocks (in bytes) |
| CHAIN_CNT           | BIGINT       | The number of row chains in the subpartition |
| AVG_ROW_LEN       | INTEGER      | The average row length in the subpartition (in bytes) |
| SAMPLE_SIZE         | BIGINT       | The number of sampled rows for statistics |
| LAST_ANALYZED      | DATE         | The time of the last statistics collection |
| INTERVAL$            | VARCHAR(1)   | Whether it is an INTERVAL partition <br>\*   Y<br>\*   N |
| SEGMENT_CREATED     | VARCHAR(1)   | Whether the segment is created<br>\*   Y<br>\*   N |