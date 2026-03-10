This view displays all database links and their corresponding statistics related to the link group on the sandbox process yex_server.

|Field |Type |Description |
|------------------------|---------|----------------------------------|
| GROUP_ID               | NUMBER   | Group ID                                 |
| GROUP_NODE_ID          | NUMBER   | Node ID within the group                 |
| INST_ID                | NUMBER   | Instance ID                              |
| EXT_XACT_ID          | INTEGER  | Link group ID                            |
| EXT_CONNECTION_COUNT | INTEGER  | The number of connections established with remote databases within the link group |
| EXT_XACT_NEXT_ID    | INTEGER  | The ID of the next link group; if the value is -1, it indicates that the current link group is in use; if the value is 1024, it indicates the last link group in the idle link group |
| EXT_XACT_IS_FREE    | BOOLEAN  | Whether the link group is in an idle state; TRUE means idle, FALSE means in use |