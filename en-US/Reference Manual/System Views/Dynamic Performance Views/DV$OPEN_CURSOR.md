This view shows the statistics of all nodes' statements in an ISC distributed cluster and the memory usage information for each statement.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| SID | SMALLINT | Session ID |
| SADDR | RAW(8) | Reserved field |
| USER\_NAME | VARCHAR(64) | Username for each statement |
| STMT\_ID | SMALLINT | ID for each statement |
| STATUS | TINYINT | Status of each statement<br>\* 0: idle <br>\* 1: prepare <br>\* 2: execute<br>\* 3: fetch<br>\* 4: free |
| PMEM\_BLOCK\_COUNT | INTEGER | Number of pages of private memory occupied by the statement |
| PMEM\_USED\_SIZE | BIGINT | Current size of private memory occupied by the statement (in bytes) |
| GMEM\_BLOCK\_COUNT | INTEGER | Number of pages of global memory occupied by the statement |
| GMEM\_USED\_SIZE | BIGINT | Current size of global memory occupied by the statement (in bytes) |
| SQL\_ID | VARCHAR(13) | Unique identifier for a SQL statement, calculated through hashing/encryption of the SQL text |
| SQL\_HASH | BIGINT | Hash value of the SQL statement, calculated from the SQL text |