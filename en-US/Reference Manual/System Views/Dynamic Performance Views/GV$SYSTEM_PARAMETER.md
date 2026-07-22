This view displays the system configuration parameter information for all instances in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                           |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                           |
| INST_ID       | NUMBER  | Instance ID                                        |
| NAME          | VARCHAR(64) | Name of the system-level configuration parameter   |
| VALUE         | VARCHAR(4096) | Current effective value of the system-level configuration parameter. If the parameter also supports session-level modifications, the displayed value may not necessarily be the value currently applied in the session, which is different from V$PARAMETER. |
| DEFAULT_VALUE | VARCHAR(4096) | Default value of the parameter                     |
| IS_DEPRECATED | VARCHAR(8) | Whether the parameter is deprecated                 |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. | 
| ISDEFAULT | VARCHAR(8) | Whether the parameter is the system default value at instance startup<br/> * TRUE: Yes <br/> * FALSE: No, indicates the parameter has been explicitly configured in the configuration parameter file |
| ISSES_MODIFIABLE | VARCHAR(8) | Whether the parameter can be modified using ALTER SESSION statement |
| ISSYS_MODIFIABLE | VARCHAR(16) | When the modification takes effect when the parameter is modified using ALTER SYSTEM statement<br>* IMMEDIATE: the change takes effect immediately (including the current session)<br>* FALSE: the change takes effect only after restart; when modifying such parameters, scope=spfile must be specified |
| ISMODIFIED | VARCHAR(16) | Whether the parameter has been modified after the instance started<br>* MODIFIED: the parameter was modified using ALTER SESSION statement<br>* SYSTEM_MOD: the parameter was modified using ALTER SYSTEM statement<br>* FALSE: the parameter has not been modified since instance startup |

> **Note**:
>
> For floating-point type parameters, the value displayed in this view is the original string configured for the parameter. The value displayed in the V$PARAMETER view is the result of converting the actual effective numeric value in the system to a string.

***Example***

```sql
-- bloom_filter_factor is a floating-point type configuration parameter
ALTER SYSTEM SET bloom_filter_factor=0.00;

SELECT NAME,VALUE,DEFAULT_VALUE,IS_DEPRECATED FROM V$SYSTEM_PARAMETER WHERE NAME = 'BLOOM_FILTER_FACTOR';
NAME                     VALUE              DEFAULT_VALUE        IS_DEPRECATED 
------------------------ ------------------ -------------------- ------------- 
BLOOM_FILTER_FACTOR      0.00               .3                   FALSE      

SELECT NAME,VALUE,DEFAULT_VALUE,IS_DEPRECATED FROM V$PARAMETER WHERE NAME = 'BLOOM_FILTER_FACTOR';
NAME                     VALUE              DEFAULT_VALUE        IS_DEPRECATED 
------------------------ ------------------ -------------------- ------------- 
BLOOM_FILTER_FACTOR      0                  .3                   FALSE  
```
