This view displays resource management information executed in parallel by all nodes in a distributed cluster.

|Field |Type |Description |
|----------------| --- | --- |
| GROUP_ID      | INTEGER | Group ID     |
| GROUP_NODE_ID| INTEGER | Node ID within the group |
| CONSUMER_GROUP  | VARCHAR(64) | Resource group |
| PX_RES_TYPE     | VARCHAR(64) | Resource type |
| PX_RES_LIMIT    | INTEGER | Resource usage limit |
| MAX_PX_RES_USAGE| INTEGER | Maximum resource usage |
| CURR_PX_RES_USAGE| INTEGER | Current resource usage |