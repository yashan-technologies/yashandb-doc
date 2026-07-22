This view displays a summary of all configuration parameters.

|Field |Type |Description |
| --- | --- | --- |
| NAME           | VARCHAR(64)   | The name of the parameter            |
| VALUE          | VARCHAR(4096) | The current value of the parameter in memory |
| DEFAULT_VALUE  | VARCHAR(4096) | The default value of the parameter   |
| IS_DEPRECATED   | VARCHAR(8)    | Whether the parameter is deprecated   |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. | 