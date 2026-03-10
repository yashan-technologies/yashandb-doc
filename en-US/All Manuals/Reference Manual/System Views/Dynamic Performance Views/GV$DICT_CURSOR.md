This view displays information about the cursors currently in use.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER     | Group ID                                                                                        |
| GROUP_NODE_ID| NUMBER     | Node ID within the group                                                                        |
| INST_ID      | NUMBER     | Instance ID                                                                                     |
| SID          | INTEGER    | Session ID                                                                                     |
| GLOBAL       | VARCHAR(8) | Whether it is a global cursor<br>*   TRUE<br>*   FALSE                                          |
| SQL_ID       | VARCHAR(13)| SQL ID of the opened cursor<br/>* -1: Default SQL ID, a newly allocated cursor that has not been used<br/>* -2: Cursor for opening internal system tables<br/>* -3: Cursor for opening internal user tables<br/>* other: Cursor opened through SQL, corresponding SQL statement can be obtained via join with V$SQLTEXT |
| TYPE         | VARCHAR(32)| Cursor type, indicating the type of object the cursor is opened for                            |
| CONTEXT      | VARCHAR(2048)| Context information related to the cursor, reserved field, default is empty                    |