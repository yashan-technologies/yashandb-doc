本视图显示所有节点的[隐藏参数](../../隐藏参数)汇总信息。

|  字段| 类型| 说明|
|---------------|---------------|-----------|
| GROUP_ID      | NUMBER        | 组ID       |
| GROUP_NODE_ID | NUMBER        | 组内节点ID    |
| INST_ID       | NUMBER        | 实例ID      |
| NAME          | VARCHAR(64)   | 参数的名称     |
| VALUE         | VARCHAR(4096) | 当前会话下的参数值 |
| DEFAULT_VALUE | VARCHAR(4096) | 参数的默认值    |
| IS_DEPRECATED | VARCHAR(8)    | 参数是否过时    |
| ISPDB_MODIFIABLE | VARCHAR(5) | 参数是否能在PDB中直接修改。TRUE表示该参数能在PDB中直接修改，FALSE表示该参数需要在CDB$ROOT中修改 |
| ISPDB_PRIVATE | VARCHAR(5) | 参数是否为私有参数。修改私有参数只会影响当前PDB不会影响其他PDB |
