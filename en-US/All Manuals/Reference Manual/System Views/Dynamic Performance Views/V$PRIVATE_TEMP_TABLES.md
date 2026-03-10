This view displays summary information for all private temporary tables.

|Field |Type |Description |
| --- | --- | --- |
| SID              | SMALLINT   | Session ID                                                                                                                                          |
| SERIAL#         | BIGINT     | The ID of the private temporary table. This ID is independent of non-temporary table IDs, so there may be cases where a non-temporary table ID and a private temporary table ID are the same. |
| OWNER            | VARCHAR(64)| User name                                                                                                                                          |
| TABLE_NAME       | VARCHAR(64)| Table name                                                                                                                                         |
| TABLESPACE_NAME  | VARCHAR(64)| Tablespace name                                                                                                                                   |
| DURATION         | VARCHAR(16)| Lifecycle (session-level, transaction-level)                                                                                                       |
| NUM_ROWS         | INTEGER    | Number of rows in the table (reserved field)                                                                                                      |
| BLOCKS           | INTEGER    | Number of blocks in the table (reserved field)                                                                                                     |
| AVG_ROW_LEN      | INTEGER    | Average row length (in bytes) (reserved field)                                                                                                    |
| LAST_ANALYZED    | DATE       | Last time statistics were collected (reserved field)                                                                                              |
| TXN_ID           | BIGINT     | Transaction ID of the transaction-level private temporary table (reserved field)                                                                    |