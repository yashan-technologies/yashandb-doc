本视图显示所有Database Link执行事务的状态。

|  字段| 类型| 描述|
|-------------------|-------------|--|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| DB_LINK | VARCHAR(64) | Database Link的名称 |
| IN_TRANSACTION | VARCHAR(3) | 当前Database Link是否正在执行事务 |
