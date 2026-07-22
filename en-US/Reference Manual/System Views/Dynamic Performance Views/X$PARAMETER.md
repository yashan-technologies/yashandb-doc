This view displays the summary information of all [hidden parameters](../../Hidden Configuration Parameters).  
|Field |Type |Description |
|---------------|---------------|-----------|
| NAME           | VARCHAR(64)    | The name of the parameter |
| VALUE          | VARCHAR(4096)  | The current value of the parameter in memory |
| DEFAULT_VALUE  | VARCHAR(4096)  | The default value of the parameter |
| IS_DEPRECATED   | VARCHAR(8)     | Whether the parameter is deprecated |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. |  
| ISDEFAULT | VARCHAR(8) | Whether the parameter is the system default value at instance startup<br/> * TRUE: Yes <br/> * FALSE: No, indicates the parameter has been explicitly configured in the configuration parameter file |
| ISSES_MODIFIABLE | VARCHAR(8) | Whether the parameter can be modified using ALTER SESSION statement |
| ISSYS_MODIFIABLE | VARCHAR(16) | When the modification takes effect when the parameter is modified using ALTER SYSTEM statement<br>* IMMEDIATE: the change takes effect immediately (including the current session)<br>* FALSE: the change takes effect only after restart; when modifying such parameters, scope=spfile must be specified |
| ISMODIFIED | VARCHAR(16) | Whether the parameter has been modified after the instance started<br>* MODIFIED: the parameter was modified using ALTER SESSION statement<br>* SYSTEM_MOD: the parameter was modified using ALTER SYSTEM statement<br>* FALSE: the parameter has not been modified since instance startup |