This view displays information about each partition of all partition indexes.

|Field |Type |Description |
| --- | --- | --- |
| INDEX\_OWNER          | VARCHAR(64)   | The username of the index                                  |
| INDEX\_NAME           | VARCHAR(64)   | The name of the index                                      |
| COMPOSITE             | VARCHAR(1)    | Whether it is a composite partition index<br>\*   Y<br>\*   N |
| PARTITION\_NAME       | VARCHAR(64)   | The name of the index partition                            |
| SUBPARTITION\_COUNT   | BIGINT        | The number of subpartitions corresponding to the index partition |
| HIGH\_VALUE           | VARCHAR(4000) | The boundary value string of the index partition           |
| HIGH\_VALUE\_LENGTH   | INTEGER       | The length of the boundary value string of the index partition |
| PARTITION\_POSITION    | BIGINT        | The position of the index partition within the index       |
| STATUS                | VARCHAR(8)    | Whether the index partition is usable<br>\*   USABLE<br>\*   UNUSABLE<br>*   N/A: For secondary partition indexes, check the status in DBA_IND_SUBPARTITIONS |
| TABLESPACE\_NAME      | VARCHAR(64)   | The name of the tablespace where the index partition is located |
| PCT\_FREE             | INTEGER       | The percentage of space to be reserved at least within the data block |
| INI\_TRANS            | INTEGER       | The preset number of Xslots during the initialization of the data block |
| MAX\_TRANS            | INTEGER       | The maximum number of Xslots the data block can expand to  |
| LOGGING               | CHAR(1)      | Whether the index partition records redo<br>\*   Y<br>\*   N |
| BLEVEL                | INTEGER       | The number of layers in the BTree                          |
| LEAF\_BLOCKS          | BIGINT        | The number of leaf nodes in the BTree                       |
| DISTINCT\_KEYS        | BIGINT        | The number of unique key values                             |
| AVG\_LEAF\_BLOCKS\_PER\_KEY | BIGINT | The average number of leaf blocks occupied by each key value |
| AVG\_DATA\_BLOCKS\_PER\_KEY | BIGINT | The average number of blocks occupied by each key value     |
| NUM\_ROWS             | BIGINT        | The number of rows in the index                             |
| SAMPLE\_SIZE          | BIGINT        | The number of sampled rows for index partition statistics    |
| LAST\_ANALYZED       | DATE          | The time of the last execution of statistics collection      |
| INTERVAL$             | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N     |
| SEGMENT\_CREATED      | VARCHAR(3)    | Whether the segment has been created<br>\*   Y<br>\*   N<br>*   N/A |