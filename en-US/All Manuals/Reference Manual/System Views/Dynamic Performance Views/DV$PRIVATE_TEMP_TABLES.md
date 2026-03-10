This view shows the summary information of all nodes' private temporary tables in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER    | Group ID                                                                                        |
| GROUP_NODE_ID    | INTEGER    | Node ID within the group                                                                        |
| SID              | SMALLINT   | Session ID                                                                                       |
| SERIAL#          | BIGINT     | The ID of the private temporary table. This ID is independent of the non-temporary table ID, so there may be cases where the non-temporary table ID and the private temporary table ID are the same. |
| OWNER            | VARCHAR(64)| User name                                                                                       |
| TABLE_NAME       | VARCHAR(64)| Table name                                                                                      |
| TABLESPACE_NAME  | VARCHAR(64)| Tablespace name                                                                                 |
| DURATION         | VARCHAR(16)| Lifecycle (session-level, transaction-level)                                                     |
| NUM_ROWS         | INTEGER    | Number of rows in the table                                                                      |
| BLOCKS           | INTEGER    | Number of pages for the table                                                                    |
| AVG_ROW_LEN      | INTEGER    | Average row length (in bytes)                                                                    |
| LAST_ANALYZED    | DATE       | The last time statistics were collected                                                          |
| TXN_ID           | BIGINT     | Transaction ID of the transaction that created the transaction-level private temporary table     |