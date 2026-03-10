本视图显示分布式集群中所有节点的redo文件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| THREAD# | TINYINT | 实例编号 |
| ID  | INTEGER | redo文件ID |
| NAME | VARCHAR(255) | redo文件路径 |
| BLOCK_SIZE | INTEGER | redo文件的页面大小（单位：字节） |
| BLOCK_COUNT | BIGINT | redo文件总的页面数量 |
| USED_BLOCKS | BIGINT | redo文件已使用的页面数量 |
| SEQUENCE# | INTEGER | redo文件的序列号，该序列号递增 |
| STATUS | VARCHAR(8) | redo文件的状态<br>\*   NEW：新创建的redo文件，未被使用过<br>\*   INACTIVE：该状态下的redo文件包含的redo对应的页面都已经写入磁盘。此状态下的redo文件可以被复用<br>\*   ACTIVE：该状态下的redo文件包含的redo对应的页面存在未写入磁盘的情况，或者该redo还没被归档。因此，该状态下的文件不能被复用<br>\*   CURRENT：当前正在使用的redo文件 |
| TYPE | VARCHAR(8) | redo文件的类型<br>\*   ONLINE：表示为ONLINE文件，用于存储主库产生的redo<br>\*   STANDBY：表示为STANDBY文件，用于存储逻辑备库产生的redo文件 |
