This view displays the relevant statistics of objects created by database links in the sandbox process yex_server.

|Field |Type |Description |
|-------------------|-------------|--|
| GROUP_ID         | NUMBER  | Group ID                                        |
| GROUP_NODE_ID    | NUMBER  | Node ID within the group                        |
| INST_ID          | NUMBER  | Instance ID                                     |
| EXT_OBJ_ID       | INTEGER | Object ID                                       |
| EXT_OBJ_NAME     | VARCHAR(64) | Object Name                                   |
| EXT_OBJ_USERID   | INTEGER | Object USERID                                   |
| EXT_OBJ_VALID    | BOOLEAN | Whether the object is valid, TRUE indicates valid, FALSE indicates invalid |
| EXT_OBJ_REF      | INTEGER | Count of how many times the object is referenced |