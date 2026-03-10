This view displays a summary of the [hidden parameters](../../Hidden Configuration Parameters) for all nodes.

|Field |Type |Description |
|---------------|---------------|-----------|
| GROUP_ID       | NUMBER        | Group ID                     |
| GROUP_NODE_ID  | NUMBER        | Node ID within the group     |
| INST_ID        | NUMBER        | Instance ID                  |
| NAME           | VARCHAR(64)   | Name of the parameter        |
| VALUE          | VARCHAR(4096) | Parameter value for the current session |
| DEFAULT_VALUE  | VARCHAR(4096) | Default value of the parameter|
| IS_DEPRECATED   | VARCHAR(8)    | Whether the parameter is deprecated |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. | 