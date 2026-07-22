This view displays the global resource status.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER   | Group ID                                                       |
| GROUP_NODE_ID    | NUMBER   | Node ID within the group                                       |
| INST_ID          | NUMBER   | Instance ID                                                    |
| DATA_OBJECT_ID   | BIGINT   | Data object ID corresponding to the affinity policy           |
| POLICY           | VARCHAR(8) | Affinity policy: <br/> * DEFAULT is the default policy, distributed by GHT <br/> * AFFINITY, specifies instance affinity <br/> * AUTO, automatic affinity, currently a reserved type |
| MASTER           | SMALLINT | Affinity instance                                              |
| CURRENT_MASTER   | SMALLINT | Current instance where the obj resides (when the affinity instance is offline, it will be managed to other online instances) |
| PREVIOUS_MASTER  | SMALLINT | Previous affinity instance                                      |
| STATUS           | SMALLINT | Current resource migration status                               |