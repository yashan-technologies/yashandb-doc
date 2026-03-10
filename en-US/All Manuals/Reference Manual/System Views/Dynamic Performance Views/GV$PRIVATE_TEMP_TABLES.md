This view displays the summary information of all private temporary tables.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER    | Group ID                                                                                                        |
| GROUP_NODE_ID    | NUMBER    | Node ID within the group                                                                                       |
| INST_ID          | NUMBER    | Instance ID                                                                                                     |
| SID              | SMALLINT  | Session ID                                                                                                      |
| SERIAL#          | BIGINT    | ID of the private temporary table. This ID is independent of the non-temporary table ID, so it is possible for a non-temporary table ID and private temporary table ID to be the same. |
| OWNER            | VARCHAR(64)| User name                                                                                                      |
| TABLE_NAME       | VARCHAR(64)| Table name                                                                                                     |
| TABLESPACE_NAME  | VARCHAR(64)| Tablespace name                                                                                                |
| DURATION         | VARCHAR(16)| Lifecycle (session level, transaction level)                                                                    |
| NUM_ROWS         | INTEGER    | Number of rows in the table (reserved field)                                                                  |
| BLOCKS           | INTEGER    | Number of pages in the table (reserved field)                                                                  |
| AVG_ROW_LEN      | INTEGER    | Average row length (in bytes) (reserved field)                                                                 |
| LAST_ANALYZED    | DATE      | Last time statistics were collected (reserved field)                                                           |
| TXN_ID           | BIGINT    | Transaction ID for the creation of the transaction-level private temporary table (reserved field)               |