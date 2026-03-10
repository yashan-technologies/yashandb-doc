This view shows the messages that resources are currently waiting to process.

|Field |Type |Description |
|----------------|--------------|-----------------------------------------------------------|
| GROUP_ID       | NUMBER        | Group ID                                                  |
| GROUP_NODE_ID  | NUMBER        | Node ID within the group                                  |
| INST_ID        | NUMBER        | Instance ID                                               |
| RESOURCE_NAME   | VARCHAR(128)  | Resource name, block resource [space] [file] [id], lock resource [id] [type], gtid resource [len] [string] |
| TYPE           | INTEGER       | Request type                                             |
| INSTANCE_ID    | INTEGER       | Node ID that sent the request message                     |
| SESSION_ID     | INTEGER       | Session ID that sent the request message                   |
| SERIAL_NO      | INTEGER       | Sequence number of the request message                     |
| IN_PROCESS      | BOOLEAN       | Whether the current request is being processed             |