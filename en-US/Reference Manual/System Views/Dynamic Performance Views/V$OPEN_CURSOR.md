View the information related to each statement and the usage of the public memory pool.

|Field |Type |Description |
| --- | --- | --- |
| SID | SMALLINT | Session ID |
| SADDR | RAW(8) | Reserved field |
| USER\_NAME | VARCHAR(64) | Username for each statement |
| STMT\_ID | SMALLINT | ID for each statement |
| STATUS | TINYINT | Status of each statement<br>\* 0: idle <br>\* 1: prepare <br>\* 2: execute<br>\* 3: fetch<br>\* 4: free |
| PMEM\_BLOCK\_COUNT | INTEGER | Number of pages of private memory occupied by the statement |
| PMEM\_USED\_SIZE | BIGINT | Current space size of private memory occupied by the statement (unit: bytes) |
| GMEM\_BLOCK\_COUNT | INTEGER | Number of pages of global memory occupied by the statement |
| GMEM\_USED\_SIZE | BIGINT | Current space size of global memory occupied by the statement (unit: bytes) |
| SQL\_ID | VARCHAR(13) | ID value that uniquely identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| SQL\_HASH | BIGINT | Hash value of the SQL statement, computed from the SQL text |
| CURSOR\_TYPE | VARCHAR(64) | Cursor type<br>\* OPEN PL/SQL: open cursor in PL<br>\* OPEN: open cursor<br>\* SESSION CURSOR CACHED: session cached cursor<br>\* PL/SQL CURSOR CACHED: session cached cursor used in PL |