This view shows the global resource situation of YAC.

|Field |Type |Description |
| --- | --- | --- |
| DATA_OBJECT_ID   | BIGINT    | The data object ID corresponding to the affinity policy      |
| POLICY           | VARCHAR(8)| Affinity policy: <br/> * DEFAULT is the default policy, which is distributed by GHT <br/> * AFFINITY specifies instance affinity <br/> * AUTO is automatic affinity, currently a reserved type |
| MASTER           | SMALLINT  | Affinity instance                                             |
| CURRENT_MASTER   | SMALLINT  | The instance where the obj is currently located (when the affinity instance is offline, it will be handled by other online instances) |
| PREVIOUS_MASTER  | SMALLINT  | The previous affinity instance                                 |
| STATUS           | SMALLINT  | Current resource migration status                             |