本视图显示所有节点内存扩展池中内存基地址池的信息。

|  字段| 类型| 说明|
|-----------------------|--------|----------------|
| GROUP_ID              | NUMBER | 组ID            |
| GROUP_NODE_ID         | NUMBER | 组内节点ID         |
| INST_ID               | NUMBER | 实例ID           |
|  HOLD\_SIZE            | BIGINT | 当前持有的内存容量（单位：字节）   |
| USING\_SIZE           | BIGINT | 已分配的内存容量（单位：字节）  |
| UNUSED\_SIZE          | BIGINT | 空闲的内存容量（单位：字节）  |
| FILL\_TIMES           | BIGINT | 缓存未命中的总次数，各个分区次数的总和 |
| MAX\_CONTINUOUS\_SIZE | BIGINT | 最大连续内存块的容量（单位：字节）  |
