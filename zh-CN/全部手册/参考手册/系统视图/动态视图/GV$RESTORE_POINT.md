本视图显示全库闪回所有还原点的信息。



存算一体分布式集群部署中无此功能，本视图无意义。



|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME | VARCHAR(68) | 还原点名称 |
| SCN | BIGINT | 还原点所代表的能闪回的scn |
| IS_GUARANTEE | TINYINT | 是否为永久还原点<br/>* 0：否<br/>*  1：是|
| CREATE_TIME | TIMESTAMP(6) | 还原点被创建时对应的timestamp |
