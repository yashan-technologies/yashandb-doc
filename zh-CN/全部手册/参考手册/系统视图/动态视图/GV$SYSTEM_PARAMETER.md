本视图显示集群中所有实例系统配置参数信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME | VARCHAR(64) | 系统级配置参数名称 |
| VALUE | VARCHAR(4096) | 当前系统级生效的配置参数值，如果参数也支持会话级修改，则显示的值不一定是当前会话正在应用的值，区别于V$PARAMETER |
| DEFAULT_VALUE | VARCHAR(4096) | 参数的默认值 |
| IS_DEPRECATED | VARCHAR(8) | 参数是否过时 |
| ISPDB_MODIFIABLE | VARCHAR(5) | 该字段仅在容器数据库中有意义<br/>参数是否能在PDB中修改<br/>* TRUE：表示该参数能在PDB中直接修改<br/>* FALSE：表示该参数只能连接根容器中修改，即该参数为全局参数 |
| ISPDB_PRIVATE | VARCHAR(5) | 该字段仅在容器数据库中有意义<br/>参数是否为私有参数，即是否为每个容器（根容器、PDB）独立的参数，修改私有参数仅对自身生效 |

> **Note**:
>
>对于浮点类型的参数，本视图显示的值为该参数配置的原始字符串，V$PARAMETER视图显示的值则为系统中实际生效的数值转为字符串后的结果。

示例

```sql
-- bloom_filter_factor是一个浮点类型的配置参数
ALTER SYSTEM SET bloom_filter_factor=0.00;

SELECT NAME,VALUE,DEFAULT_VALUE FROM V$SYSTEM_PARAMETER WHERE NAME = 'BLOOM_FILTER_FACTOR';
NAME                     VALUE              DEFAULT_VALUE         
------------------------ ------------------ -------------------- 
BLOOM_FILTER_FACTOR      0.00               .3                         

SELECT NAME,VALUE,DEFAULT_VALUE FROM V$PARAMETER WHERE NAME = 'BLOOM_FILTER_FACTOR';
NAME                     VALUE              DEFAULT_VALUE         
------------------------ ------------------ --------------------  
BLOOM_FILTER_FACTOR      0                  .3                     
```
