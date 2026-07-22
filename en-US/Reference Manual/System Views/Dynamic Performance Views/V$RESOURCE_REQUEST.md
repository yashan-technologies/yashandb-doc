This view displays the messages that the resources are currently waiting to process.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE_NAME| VARCHAR(128) | Resource name, block resource [space][file][id], lock resource [id][type], gtid resource [len][string] |
| TYPE         | INTEGER   | Request type                                                                                |
| INSTANCE_ID  | INTEGER   | The ID of the node that issued the request message                                           |
| SESSION_ID   | INTEGER   | The session ID of the request message                                                        |
| SERIAL_NO    | INTEGER   | The sequence number of the request message                                                   |
| IN_PROCESS    | BOOLEAN   | Indicates whether the current request is being processed                                      |