This view shows the segment information of global temporary tables in a database session.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | NUMBER        | Group ID                       |
| GROUP_NODE_ID     | NUMBER        | Node ID within the group       |
| INST_ID           | NUMBER        | Instance ID                    |
| TABLESPACE_NAME   | VARCHAR(64)   | Tablespace name                |
| FILE_ID           | INTEGER       | Global ID of the data file     |
| EXTENTS_CACHED    | NUMBER        | Number of extents buffered      |
| EXTENTS_USED      | NUMBER        | Number of extents used         |
| BLOCKS_CACHED     | NUMBER        | Number of blocks buffered       |
| BLOCKS_USED       | NUMBER        | Number of blocks used          |
| BYTES_CACHED      | NUMBER        | Number of bytes buffered        |
| BYTES_USED        | NUMBER        | Number of bytes used           |
| INTER_FNO        | INTEGER       | File ID of the data file within the tablespace |