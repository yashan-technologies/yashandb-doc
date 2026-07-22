本视图显示系统配置参数信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| NAME | VARCHAR(64) | 系统级配置参数名称 |
| VALUE | VARCHAR(4096) | 当前系统级生效的配置参数值，如果参数也支持会话级修改，则显示的值不一定是当前会话正在应用的值，区别于V$PARAMETER |
| DEFAULT_VALUE | VARCHAR(4096) | 参数的默认值 |
| IS_DEPRECATED | VARCHAR(8) | 参数是否过时 |
| CLUSTER_CONSISTENT | VARCHAR(8) | 共享集群/分布式集群部署中，是否要求所有实例的参数值保持一致<br>\* TRUE：要求一致，修改该参数且指定实时生效时数据库会自动将其同步到所有实例以保证全局一致性<br>\* FALSE：不要求一致，各个实例可单独自定义配置且不会自动同步 |
| ISDEFAULT | VARCHAR(8) | 实例启动时，参数是否为系统默认值<br/> * TRUE：是 <br/> * FALSE：否，说明配置参数文件中已显式配置该参数 |
| ISSES_MODIFIABLE | VARCHAR(8) | 参数是否可以通过ALTER SESSION语句修改 |
| ISSYS_MODIFIABLE | VARCHAR(16) | 参数通过ALTER SYSTEM语句修改时的生效时机<br>\* IMMEDIATE：更改立即生效（含当前会话）<br>\* FALSE：更改后必须重启生效，修改此类参数时必须指定scope=spfile |
| ISMODIFIED  | VARCHAR(16) | 参数是否在实例启动后被修改<br>\* MODIFIED：参数已使用ALTER SESSION语句修改<br>\* SYSTEM_MOD：参数已使用ALTER SYSTEM语句修改<br>\* FALSE：参数在实例启动后未被修改 |

> **Note**:
>
>对于浮点类型的参数，本视图显示的值为该参数配置的原始字符串，V$PARAMETER视图显示的值则为系统中实际生效的数值转为字符串后的结果。

示例

```sql
-- bloom_filter_factor是一个浮点类型的配置参数
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
