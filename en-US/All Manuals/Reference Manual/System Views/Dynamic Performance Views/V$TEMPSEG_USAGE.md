This view primarily displays segment information for temporary attributes.

|Field |Type |Description |
| --- | --- | --- |
| USERNAME       | VARCHAR(64)     | Username associated with the session |
| USER           | VARCHAR(64)     | Username associated with the session, equivalent to USERNAME column |
| SID            | SMALLINT        | Session ID |
| SESSION_NUM    | INTEGER         | Assigned sequence number |
| SQL_ID         | VARCHAR(13)     | SQL ID currently being executed in the session (hash/encrypted result of SQL text) |
| SQLHASH        | BIGINT          | SQL hash value currently being executed in the session (calculated from SQL text) |
| TABLESPACE     | VARCHAR(64)     | Name of the tablespace being used |
| CONTENTS       | VARCHAR(9)      | Indicates whether the tablespace is temporary. Currently always TEMPORARY |
| SEGTYPE        | VARCHAR(18)     | Type of space occupant <br/> * SORT: Sorting computation materialized area <br/> * HASH: Hash join materialized area <br/> * MERGE_SORT: Merge sorting materialized area <br/> * QUEUE: Queue materialized area <br/> * STACK: Stack materialized area <br/> * LIST: List materialized area <br/> * DATA: Temporary table data <br/> * INDEX: Temporary table index <br/> * LOB_DATA: Temporary LOB column data <br/> * LOB_INDEX: Temporary LOB column index |
| SEGFILE#       | INTEGER         | Data file ID number being used |
| SEGBLK#        | INTEGER         | If the type of space occupant is one of the following, this column value is the entry page ID of the occupied space; otherwise, this column value is null <br/> * DATA: Temporary table data <br/> * INDEX: Temporary table index <br/> * LOB_DATA: Temporary LOB column data <br/> * LOB_INDEX: Temporary LOB column index |
| EXTENTS        | BIGINT          | Number of extents being used |
| BLOCKS         | BIGINT          | Number of blocks being used |
| SEGRFNO#       | INTEGER         | Internal ID number of the data file being used |
| TS#            | SMALLINT        | Tablespace ID number being used |