This view displays the summary information of SQL statements currently being executed in sessions across all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER   | Group ID                                                                                    |
| GROUP_NODE_ID     | INTEGER   | Node ID within the group                                                                    |
| ADDRESS           | RAW(8)    | SQL address                                                                                 |
| HASH_VALUE        | BIGINT    | Hash value of the SQL, calculated from the SQL text                                        |
| SQL_ID            | VARCHAR(13) | A unique identifier for a SQL statement, obtained through hashing/encryption of the SQL text |
| COMMAND_TYPE      | INTEGER   | SQL command type<br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| PIECE             | INTEGER   | Sort order number of the SQL text                                                          |
| SQL_TEXT          | VARCHAR(1000) | The first 1000 characters of the SQL text                                                  |