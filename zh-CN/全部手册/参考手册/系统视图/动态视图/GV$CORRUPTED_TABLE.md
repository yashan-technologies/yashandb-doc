单机部署中，本视图显示所有损坏表的信息。

存算一体分布式集群部署中，除CN外，本视图显示当前实例的所有损坏表汇总信息；在CN上，本视图显示MN的所有损坏表信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| OBJ# | BIGINT | 表的objectId |
| NAME | VARCHAR(64) | 表名 |
