This view displays the summary information of the YFS memory pool (MEMORY POOL) for all nodes.

Applicable only to YAC/Distributed Cluster Deployment; this view is not meaningful in other deployment forms.

|Field |Type |Description |
|----------------|-------------|-----------------------------------------------|
| GROUP_ID       | NUMBER      | Group ID                                         |
| GROUP_NODE_ID  | NUMBER      | Node ID within the group                         |
| INST_ID        | NUMBER      | Instance ID                                      |
| NAME           | VARCHAR(68) | Name of the memory pool                          |
| TOTAL_SIZE     | BIGINT      | Total size of the memory pool (in bytes)        |
| USED_SIZE      | BIGINT      | Size of the space used in the memory pool (in bytes) |
| FREE_SIZE      | BIGINT      | Size of the unused space in the memory pool (in bytes) |
| MAX_SIZE       | BIGINT      | Maximum size of the memory pool (in bytes)      |