本视图显示存算一体分布式集群中所有节点表空间的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | 表空间的ID号 |
| NAME | VARCHAR(64) | 表空间名称 |
| STATUS | VARCHAR(8) | 表空间状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线<br>*   READ ONLY: 只读  |
| EXTENT_BLOCKS | INTEGER | 每个extent包含的block数量 |
| CONTENTS | VARCHAR(16) | 表空间的类型<br>\* SWAP：用于中间数据的换入换出<br>\* UNDO：用于保存undo数据<br>\* TEMPORARY：用于临时表数据的换入换出<br>\* PERMANENT：用于保存用户数据 |
| ALLOCATION_TYPE | VARCHAR(8) | 表空间extent分配方式<br>\* AUTO：动态扩展extent<br>\* UNIFORM：固定大小extent |
| MEMORY_MAPPED | VARCHAR(8) | 是否为内存映射表空间（数据常驻内存） |
| ENCRYPTED | VARCHAR(8) | 是否为加密表空间 |
| TEMPORARY | VARCHAR(8) | 是否为具有临时属性的表空间（数据不会持久化） |
| COMPRESSED | VARCHAR(8) | 是否为压缩表空间 |
