This view displays all SQL text-related information.

|Field |Type |Description |
| --- | --- | --- |
| ADDRESS      | RAW(8)    | SQL address                                                                                          |
| HASH_VALUE   | BIGINT    | The hash value of the SQL, calculated from the SQL text                                             |
| SQL_ID       | VARCHAR(13)| The unique identifier ID value for a SQL statement, obtained through hashing/encryption of the SQL text |
| COMMAND_TYPE | INTEGER   | The command type of the SQL<br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| PIECE        | INTEGER   | The sorting number of the SQL text                                                                    |
| SQL_TEXT     | VARCHAR(1000) | The first 1000 characters of the SQL text                                                             |