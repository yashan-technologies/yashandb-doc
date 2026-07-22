本视图显示全库闪回所有日志文件的信息。



存算一体分布式集群部署中无此功能，本视图无意义。



|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| FILE\_NAME | VARCHAR(256) | 文件名 |
| FILE\_NUM | INTEGER | 文件ID |
| THREAD# | TINYINT | 文件所属的实例ID |
| SEQUENCE# | INTEGER | 文件序列 |
| FILE\_SIZE | BIGINT | 文件所占空间大小（单位：字节） |
| FIRST\_CHANGE# | BIGINT | 文件被创建时对应的scn |
| FIRST\_TIME | TIMESTAMP(6) | 文件被创建时对应的tiemstamp |
