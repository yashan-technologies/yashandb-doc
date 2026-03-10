本视图显示全库闪回所有日志文件的信息。

仅适用于单机部署，其他部署形态中本视图无意义。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| FILE_NAME | VARCHAR(256) | 文件名 |
| FILE_NUM | INTEGER | 文件ID |
| THREAD# | TINYINT | 文件所属的实例ID |
| SEQUENCE# | INTEGER | 文件序列 |
| FILE_SIZE | BIGINT | 文件所占空间大小（单位：字节） |
| FIRST_CHANGE# | BIGINT | 文件被创建时对应的scn |
| FIRST_TIME | TIMESTAMP | 文件被创建时对应的timestamp |
