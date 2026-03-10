Fault Diagnosis View, displaying the results of relevant health checks for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER  | Group ID                                         |
| GROUP_NODE_ID        | INTEGER  | Node ID within the group                         |
| FINDING_ID           | INTEGER  | Unique ID representing the result                |
| RUN_ID               | INTEGER  | ID of the run that created this result           |
| NAME                 | VARCHAR(33) | Name of the investigation result                   |
| TIME_DETECTED        | DATE     | Time when this finding was detected              |
| DESCRIPTION          | VARCHAR(1024) | Description of the investigation result           |
| DAMAGE_DESCRIPTION    | VARCHAR(512) | Possible damage description of the investigation result |