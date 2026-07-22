This view displays system configuration parameter information.

|Field |Type |Description |
| --- | --- | --- |
| NAME    | VARCHAR(64)  | System-level configuration parameter name               |
| VALUE   | VARCHAR(4096)| Current effective value of the system-level configuration parameter. If the parameter also supports session-level modifications, the displayed value may not be the value currently applied in the session, unlike V$PARAMETER |
| DEFAULT_VALUE | VARCHAR(4096)| Default value of the parameter                          |
| IS_DEPRECATED | VARCHAR(8) | Whether the parameter is deprecated                     |
| ISPDB_MODIFIABLE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter can be modified in PDB<br/>* TRUE: indicates that the parameter can be directly modified in PDB<br/>* FALSE: indicates that the parameter can only be modified by connecting to the CDB root, i.e., the parameter is a global parameter |
| ISPDB_PRIVATE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter is a private parameter, i.e., whether it is an independent parameter for each container (the CDB root, PDB). Modifying private parameters only takes effect on themselves |
| CLUSTER_CONSISTENT | VARCHAR(8) | Whether all instance parameter values must remain consistent in YAC/Distributed Cluster  Deployment <br>* TRUE: Requires consistency. When this parameter is modified and set to take effect immediately, the database automatically synchronizes the change to all instances to ensure global consistency.  <br>* FALSE: Does not require consistency. Each instance can be configured independently without automatic synchronization. |

> **Note**:
>
> For floating-point type parameters, the value displayed in this view is the original string configured for the parameter, while the value shown in the V$PARAMETER view is the actual effective numeric value in the system converted to a string.

***Example***

```sql
-- bloom_filter_factor is a floating-point configuration parameter
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
