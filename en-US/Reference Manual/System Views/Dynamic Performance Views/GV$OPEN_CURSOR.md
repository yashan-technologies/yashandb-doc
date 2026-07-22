Check the relevant information for each statement and the situation of the public heap memory pool used.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| SID | SMALLINT | Session ID |
| SADDR | RAW(8) | Reserved field |
| USER_NAME | VARCHAR(64) | Username for each statement |
| STMT_ID | SMALLINT | ID for each statement |
| STATUS | TINYINT | Status of each statement<br>\* 0: idle <br>\* 1: prepare <br>\* 2: execute<br>\* 3: fetch<br>\* 4: free |
| PMEM_BLOCK_COUNT | INTEGER | Number of pages of private memory occupied by the statement |
| PMEM_USED_SIZE | BIGINT | Current size of private memory occupied by the statement (unit: bytes) |
| GMEM_BLOCK_COUNT | INTEGER | Number of pages of global memory occupied by the statement |
| GMEM_USED_SIZE | BIGINT | Current size of global memory occupied by the statement (unit: bytes) |
| SQL_ID | VARCHAR(13) | ID value that uniquely identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| SQL_HASH | BIGINT | Hash value of the SQL statement, calculated from the SQL text |
| CURSOR\_TYPE | VARCHAR(64) | Cursor type<br>\* OPEN PL/SQL: open cursor in PL<br>\* OPEN: open cursor<br>\* SESSION CURSOR CACHED: session cached cursor<br>\* PL/SQL CURSOR CACHED: session cached cursor used in PL |