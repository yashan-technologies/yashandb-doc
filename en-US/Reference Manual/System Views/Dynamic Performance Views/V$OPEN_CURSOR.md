View the information related to each statement and the usage of the public memory pool.

|Field |Type |Description |
| --- | --- | --- |
| SID | SMALLINT | Session ID |
| SADDR | RAW(8) | Reserved field |
| USER_NAME | VARCHAR(64) | Username for each statement |
| STMT_ID | SMALLINT | ID for each statement |
| STATUS | TINYINT | Status of each statement<br>\* 0: idle <br>\* 1: prepare <br>\* 2: execute<br>\* 3: fetch<br>\* 4: free |
| PMEM_BLOCK_COUNT | INTEGER | Number of pages of private memory occupied by the statement |
| PMEM_USED_SIZE | BIGINT | Current space size of private memory occupied by the statement (unit: bytes) |
| GMEM_BLOCK_COUNT | INTEGER | Number of pages of global memory occupied by the statement |
| GMEM_USED_SIZE | BIGINT | Current space size of global memory occupied by the statement (unit: bytes) |
| SQL_ID | VARCHAR(13) | ID value that uniquely identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| SQL_HASH | BIGINT | Hash value of the SQL statement, computed from the SQL text |