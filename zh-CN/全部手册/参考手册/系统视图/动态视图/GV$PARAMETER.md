本视图显示集群中所有实例所有配置参数汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME | VARCHAR(64) | 参数的名称 |
| VALUE | VARCHAR(4096) | 当前内存中参数的值 |
| DEFAULT_VALUE | VARCHAR(4096) | 参数的默认值 |
| IS_DEPRECATED | VARCHAR(8) | 参数是否过时 |
| ISPDB_MODIFIABLE | VARCHAR(5) | 该字段仅在容器数据库中有意义<br/>参数是否能在PDB中修改<br/>* TRUE：表示该参数能在PDB中直接修改<br/>* FALSE：表示该参数只能连接根容器中修改，即该参数为全局参数 |
| ISPDB_PRIVATE | VARCHAR(5) | 该字段仅在容器数据库中有意义<br/>参数是否为私有参数，即是否为每个容器（CDB root、PDB）独立的参数，修改私有参数仅对自身生效 |
