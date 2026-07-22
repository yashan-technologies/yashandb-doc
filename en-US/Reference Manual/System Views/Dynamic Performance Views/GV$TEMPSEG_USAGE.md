This view mainly displays the segment information of temporary attributes.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID    | NUMBER     | Group ID                                       |
| GROUP_NODE_ID | NUMBER   | Node ID within the group                       |
| INST_ID     | NUMBER     | Instance ID                                    |
| USERNAME    | VARCHAR(64)| Session associated username                    |
| USER        | VARCHAR(64)| Session associated username, equivalent to USERNAME column |
| SID         | SMALLINT   | Session ID                                     |
| SESSION_NUM | INTEGER    | Assigned sequence number                        |
| SQL_ID      | VARCHAR(13)| SQL ID being executed by the session (result of hashing/encrypting SQL text) |
| SQLHASH     | BIGINT     | SQL hash value of the SQL being executed (calculated from SQL text) |
| TABLESPACE  | VARCHAR(64)| Name of the tablespace being used              |
| CONTENTS    | VARCHAR(9) | Whether the tablespace is a temporary attribute. Currently always constant as TEMPORARY |
| SEGTYPE     | VARCHAR(18)| Type of space occupier <br/> * SORT: Sort computation materialized area <br/> * HASH: Hash join materialized area <br/> * MERGE_SORT: Merge sort materialized area <br/> * QUEUE: Queue materialized area <br/> * STACK: Stack materialized area <br/> * LIST: List materialized area <br/> * DATA: Temporary table data <br/> * INDEX: Temporary table index <br/> * LOB_DATA: Temporary LOB column data <br/> * LOB_INDEX: Temporary LOB column index |
| SEGFILE#    | INTEGER    | Data file ID number being occupied              |
| SEGBLK#     | INTEGER    | If the space occupier is one of the following types, this column value is the entry page ID of the occupied space; otherwise, this column value is null <br/> * DATA: Temporary table data <br/> * INDEX: Temporary table index <br/> * LOB_DATA: Temporary LOB column data <br/> * LOB_INDEX: Temporary LOB column index |
| EXTENTS     | BIGINT     | Number of extents being occupied                |
| BLOCKS      | BIGINT     | Number of blocks being occupied                 |
| SEGRFNO#    | INTEGER    | Internal ID number within the data file being occupied |
| TS#         | SMALLINT   | Tablespace ID number being occupied            |