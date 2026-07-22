In Standalone Deployment, this view displays information about all damaged tables. 

In ISC Distributed Cluster Deployment, this view shows a summary of all damaged tables for the current instance except for CN; on CN, this view displays information about all damaged tables of MN.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID    | NUMBER  | Group ID         |
| GROUP_NODE_ID| NUMBER | Node ID within the group |
| INST_ID     | NUMBER  | Instance ID      |
| OBJ#        | BIGINT  | Object ID of the table |
| NAME        | VARCHAR(64) | Table name     |