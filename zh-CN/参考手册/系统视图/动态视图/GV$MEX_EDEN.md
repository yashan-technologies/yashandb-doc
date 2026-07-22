本视图显示所有节点内存扩展池中伊甸园（Eden Space）的信息。

|  字段| 类型| 说明|
|----------------------|--------|----------------------------------------------------------------------------------------------------|
| GROUP_ID             | NUMBER | 组ID                                                                                                |
| GROUP_NODE_ID        | NUMBER | 组内节点ID                                                                                             |
| INST_ID              | NUMBER | 实例ID                                                                                               |
|  HOLD\_SIZE           | BIGINT | 内存扩展池的内存总容量（单位：字节） |
| USING\_SIZE          | BIGINT | 已分配的内存容量（单位：字节）<br/> 恒等于V$MEX_BASE视图中的持有内存HOLD_SIZE           |
| UNUSED\_SIZE         | BIGINT | 暂未分配或已回收的空闲内存容量（单位：字节）              |
| EMAP\_SIZE           | BIGINT | 内存扩展池的页表所占内存容量（单位：字节）                    |
| EXTENT\_HOLD\_SIZE   | BIGINT |  可存储内存扩展池元数据的内存总量（单位：字节）              |
| EXTENT\_USING\_SIZE  | BIGINT | 存储内存扩展池元数据已用的内存容量（单位：字节）            |
| EXTENT\_UNUSED\_SIZE | BIGINT | 可存储内存扩展池元数据的剩余内存容量（单位：字节）        |
