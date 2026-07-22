This view displays the partition information of all partitioned tables.

|Field |Type |Description |
| --- | --- | --- |
| TABLE\_OWNER         | VARCHAR(64)   | Username of the table                             |
| TABLE\_NAME         | VARCHAR(64)   | Name of the table to which the partition belongs  |
| COMPOSITE            | VARCHAR(1)    | Whether it is a composite partition               |
| PARTITION\_NAME      | VARCHAR(64)   | Name of the partition                             |
| SUBPARTITION\_COUNT  | BIGINT        | Number of subpartitions in the current partition  |
| HIGH\_VALUE          | VARCHAR(4000) | String of the partition boundary value            |
| HIGH\_VALUE\_LENGTH  | INTEGER       | Length of the partition boundary value string (in bytes) |
| PARTITION\_POSITION   | BIGINT        | Position of the partition within the table       |
| TABLESPACE\_NAME     | VARCHAR(64)   | Name of the tablespace where the partition is located |
| PCT\_FREE            | INTEGER       | Percentage of space reserved within data pages    |
| INI\_TRANS           | INTEGER       | Number of Xslots reserved when initializing data pages |
| MAX\_TRANS           | INTEGER       | Maximum number of Xslots that can be extended in data pages |
| LOGGING              | CHAR(1)      | Whether the partition is logging redo<br>\*   Y<br>\*   N |
| NUM\_ROWS            | BIGINT        | Number of rows in the partition                   |
| BLOCKS               | BIGINT        | Number of blocks in the partition                 |
| EMPTY\_BLOCKS        | BIGINT        | Number of empty blocks in the partition           |
| AVG\_SPACE           | INTEGER       | Average free space in partition blocks (in bytes) |
| CHAIN\_CNT           | BIGINT        | Number of row links in the partition              |
| AVG\_ROW\_LEN       | INTEGER       | Average row length in the partition (in bytes)   |
| SAMPLE\_SIZE         | BIGINT        | Number of sampled rows for statistics             |
| LAST\_ANALYZED      | DATE          | Last time statistics were collected               |
| INTERVAL$            | VARCHAR(1)    | Whether it is an INTERVAL partition<br>\*   Y<br>\*   N |
| SEGMENT\_CREATED     | VARCHAR(1)    | Whether the segment is created<br>\*   Y<br>\*   N |