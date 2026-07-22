This view displays information about each secondary partition of all composite partition indexes.

|Field |Type |Description |
| --------------------------- | ------------- | ------------------------------------------------ |
| INDEX\_OWNER                | VARCHAR(64)   | Username of the index                             |
| INDEX\_NAME                 | VARCHAR(64)   | Index name                                       |
| PARTITION\_NAME             | VARCHAR(64)   | Name of the primary partition                     |
| SUBPARTITION\_NAME          | VARCHAR(64)   | Name of the secondary partition                   |
| HIGH\_VALUE                 | VARCHAR(4000) | Boundary value string of the secondary partition   |
| HIGH\_VALUE\_LENGTH         | INTEGER       | Length of the boundary value string of the secondary partition |
| PARTITION\_POSITION         | BIGINT        | Position of the partition within the index        |
| SUBPARTITION\_POSITION      | BIGINT        | Position of the secondary partition within the primary partition |
| STATUS                      | VARCHAR(8)    | Availability of the partition<br>\*   USABLE<br>\*   UNUSABLE |
| TABLESPACE\_NAME            | VARCHAR(64)   | Name of the tablespace where the secondary partition is located |
| PCT\_FREE                   | INTEGER       | Percent of space reserved in data blocks         |
| INI\_TRANS                  | INTEGER       | Number of Xslots preset during page initialization |
| MAX\_TRANS                  | INTEGER       | Maximum number of Xslots the data page can expand to |
| LOGGING                     | CHAR(1)       | Whether the partition logs redo<br>\*   Y<br>\*   N         |
| BLEVEL                      | INTEGER       | Number of levels in the BTree                    |
| LEAF\_BLOCKS                | BIGINT        | Number of leaf nodes in the BTree                |
| DISTINCT\_KEYS              | BIGINT        | Number of distinct key values                     |
| AVG\_LEAF\_BLOCKS\_PER\_KEY | BIGINT        | Average number of leaf blocks per key            |
| AVG\_DATA\_BLOCKS\_PER\_KEY | BIGINT        | Average number of blocks per key                  |
| NUM\_ROWS                   | BIGINT        | Number of rows in the index                       |
| SAMPLE\_SIZE                | BIGINT        | Number of sample rows for index partition statistics |
| LAST\_ANALYZED              | DATE          | Time of the last statistics collection             |
| INTERVAL$                   | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N             |
| SEGMENT\_CREATED            | VARCHAR(1)    | Whether the segment is created<br>\*   Y<br>\*   N              |