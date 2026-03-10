This view shows the statistics of all nodes' statements in an ISC distributed cluster and the memory usage information for each statement.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| SID | SMALLINT | Session ID |
| SADDR | RAW(8) | Reserved field |
| USER_NAME | VARCHAR(64) | Username for each statement |
| STMT_ID | SMALLINT | ID for each statement |
| STATUS | TINYINT | Status of each statement<br>\* 0: idle <br>\* 1: prepare <br>\* 2: execute<br>\* 3: fetch<br>\* 4: free |
| PMEM_BLOCK_COUNT | INTEGER | Number of pages of private memory occupied by the statement |
| PMEM_USED_SIZE | BIGINT | Current size of private memory occupied by the statement (in bytes) |
| GMEM_BLOCK_COUNT | INTEGER | Number of pages of global memory occupied by the statement |
| GMEM_USED_SIZE | BIGINT | Current size of global memory occupied by the statement (in bytes) |
| SQL_ID | VARCHAR(13) | Unique identifier for a SQL statement, calculated through hashing/encryption of the SQL text |
| SQL_HASH | BIGINT | Hash value of the SQL statement, calculated from the SQL text |