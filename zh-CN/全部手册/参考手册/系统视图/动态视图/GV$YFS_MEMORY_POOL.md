本视图显示所有节点的YFS内存池（MEMORY POOL）汇总信息。

仅适用于共享集群/分布式集群部署，其他部署形态中本视图无意义。

|  字段| 类型| 说明|
|----------------|-------------|-----------------------------------------------|
| GROUP_ID       | NUMBER      | 组ID                                          |
| GROUP_NODE_ID  | NUMBER      | 组内节点ID                                     |
| INST_ID        | NUMBER      | 实例ID                                         |
| NAME           | VARCHAR(68) | 内存池的名字                                    |
| TOTAL_SIZE     | BIGINT      | 内存池的总大小（单位：字节）                     |
| USED_SIZE      | BIGINT      | 内存池中被使用的空间大小（单位：字节）            |
| FREE_SIZE      | BIGINT      | 内存池中未被使用的空间大小（单位：字节）          |
| MAX_SIZE       | BIGINT      | 内存池最大大小（单位：字节）                     |
