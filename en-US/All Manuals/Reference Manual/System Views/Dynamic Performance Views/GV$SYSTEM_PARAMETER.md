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
