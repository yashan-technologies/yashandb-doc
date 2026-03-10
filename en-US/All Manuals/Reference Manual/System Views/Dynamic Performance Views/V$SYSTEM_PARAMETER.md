This view displays system configuration parameter information.

|Field |Type |Description |
| --- | --- | --- |
| NAME    | VARCHAR(64)  | System-level configuration parameter name               |
| VALUE   | VARCHAR(4096)| Current effective value of the system-level configuration parameter. If the parameter also supports session-level modifications, the displayed value may not be the value currently applied in the session, unlike V$PARAMETER |
| DEFAULT_VALUE | VARCHAR(4096)| Default value of the parameter                          |
| IS_DEPRECATED | VARCHAR(8) | Whether the parameter is deprecated                     |

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
