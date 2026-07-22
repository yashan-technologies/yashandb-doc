This view displays summary information of currently executing SQL statements.

|Column |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER     | Group ID                                           |
| GROUP_NODE_ID| NUMBER     | Node ID within the group                          |
| INST_ID      | NUMBER     | Instance ID                                       |
| ADDRESS      | RAW(8)     | SQL Address                                       |
| HASH_VALUE   | BIGINT     | The hash value of the SQL, calculated from the SQL text |
| SQL_ID       | VARCHAR(13)| The ID that uniquely identifies a SQL statement, obtained through hash/encryption of the SQL text |
| COMMAND_TYPE | INTEGER    | Type of the SQL command                           <br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| PIECE        | INTEGER    | The sorting number of the SQL text                |
| SQL_TEXT     | VARCHAR(1000) | Slice the SQL text by 1000 bytes, and the corresponding text fragment sequence number is the PIECE field value          |