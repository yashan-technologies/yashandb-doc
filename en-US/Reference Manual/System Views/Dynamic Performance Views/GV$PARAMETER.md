This view shows a summary of all configuration parameters for all instances in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER      | Group ID               |
| GROUP_NODE_ID | NUMBER      | Node ID within the group |
| INST_ID       | NUMBER      | Instance ID            |
| NAME          | VARCHAR(64) | Name of the parameter   |
| VALUE         | VARCHAR(4096) | Current value of the parameter in memory |
| DEFAULT_VALUE | VARCHAR(4096) | Default value of the parameter |
| IS_DEPRECATED  | VARCHAR(8)  | Whether the parameter is deprecated |
| ISPDB_MODIFIABLE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter can be modified in PDB<br/>* TRUE: indicates that the parameter can be directly modified in PDB<br/>* FALSE: indicates that the parameter can only be modified by connecting to the CDB root, i.e., the parameter is a global parameter |
| ISPDB_PRIVATE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter is a private parameter, i.e., whether it is an independent parameter for each container (the CDB root, PDB). Modifying private parameters only takes effect on themselves |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. |
