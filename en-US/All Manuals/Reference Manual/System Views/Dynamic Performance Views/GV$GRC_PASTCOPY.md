This view shows the PAST COPY BLOCK information (when multiple instances hold dirty blocks simultaneously, only the latest version of one instance has write privilege, and the dirty blocks held by historical version instances that do not have write privilege are called PAST COPY BLOCK).

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER  | Group ID                                                          |
| GROUP_NODE_ID  | NUMBER  | Node ID within the group                                          |
| INST_ID        | NUMBER  | Instance ID                                                       |
| TS#            | INTEGER | Page space id                                                    |
| FILE#          | INTEGER | Page file id                                                     |
| BLK#           | INTEGER | Page id                                                          |
| RESOURCE_NAME   | VARCHAR(128) | Resource name, block resource [space][file][id]              |
| INSTANCE_ID    | TINYINT | Node holding the PAST COPY BLOCK                                  |
| LSN            | BIGINT  | LSN (Log Sequence Number) of the PAST COPY BLOCK                |