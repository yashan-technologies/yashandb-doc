This view displays all subpartition information.

|Field |Type |Description |
| --- | --- | --- |
| TABLE\_OWNER         | VARCHAR(64)  | The username of the table            |
| TABLE\_NAME          | VARCHAR(64)  | The name of the table to which the partition belongs |
| PARTITION\_NAME      | VARCHAR(64)  | The name of the partition            |
| SUBPARTITION\_NAME   | VARCHAR(64)  | The name of the subpartition         |
| HIGH\_VALUE          | VARCHAR(4000)| The boundary value string of the subpartition |
| HIGH\_VALUE\_LENGTH  | INTEGER      | The length of the subpartition boundary value string (in bytes) |
| PARTITION\_POSITION   | BIGINT       | The position of the partition within the table |
| SUBPARTITION\_POSITION| BIGINT       | The position of the subpartition within the table |
| TABLESPACE\_NAME     | VARCHAR(64)  | The tablespace name where the subpartition is located |
| PCT\_FREE            | INTEGER      | The percentage of space reserved in data pages |
| INI\_TRANS           | INTEGER      | The number of Xslots reserved when initializing the data page |
| MAX\_TRANS           | INTEGER      | The maximum number of Xslots to which the data page can expand |
| LOGGING              | CHAR(1)     | Whether the partition records redo<br>\*   Y<br>\*   N |
| NUM\_ROWS            | BIGINT       | The number of rows in the subpartition |
| BLOCKS               | BIGINT       | The number of blocks in the subpartition |
| EMPTY\_BLOCKS        | BIGINT       | The number of empty blocks in the subpartition |
| AVG\_SPACE           | INTEGER      | The average free space in subpartition blocks (in bytes) |
| CHAIN\_CNT           | BIGINT       | The number of row chains in the subpartition |
| AVG\_ROW\_LEN       | INTEGER      | The average row length in the subpartition (in bytes) |
| SAMPLE\_SIZE         | BIGINT       | The number of sampled rows for statistics |
| LAST\_ANALYZED      | DATE         | The time of the last statistics collection |
| INTERVAL$            | VARCHAR(1)   | Whether it is an INTERVAL partition <br>\*   Y<br>\*   N |
| SEGMENT\_CREATED     | VARCHAR(1)   | Whether the segment is created<br>\*   Y<br>\*   N |