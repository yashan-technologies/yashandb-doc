This view shows the summarized memory usage information of custom objects in all PL Pools.

|Field |Type |Description |
|-------|-----------|------|
| GROUP_ID   | NUMBER    | Group ID                                      |
| GROUP_NODE_ID | NUMBER | Node ID within the group                      |
| INST_ID    | NUMBER    | Instance ID                                   |
| OID        | BIGINT    | Object Identifier                             |
| VERSION     | INTEGER   | Version number of the object in memory       |
| BLOCKS     | SMALLINT  | Number of memory blocks occupied              |
| REF_COUNT  | INTEGER   | Internal reference count                       |
| TOUCHS     | INTEGER   | Access usage count                            |
| STATUS     | TINYINT   | Object status <br/>* 0: NO_READY status, <br/>* 1: READY status |
| TYPE       | TINYINT   | Object type <br/>* 0: Function, includes methods as Object type body <br/>* 1: Procedure <br/>* 2: Package head <br/>* 3: Trigger <br/>* 5: Package body |