本视图显示所有redo文件信息。

共享集群下，显示（在线实例数 * 所有实例redo文件）信息。

|  列名| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| THREAD# | TINYINT | 实例编号 |
| ID  | INTEGER | redo文件ID |
| NAME | VARCHAR(255) | redo文件路径 |
| BLOCK_SIZE | INTEGER | redo文件的页面大小（单位：字节） |
| BLOCK_COUNT | BIGINT | redo文件总的页面数量 |
| USED_BLOCKS | BIGINT | redo文件已使用的页面数量 |
| SEQUENCE# | INTEGER | redo文件的序列号，该序列号递增 |
| STATUS | VARCHAR(8) | redo文件的状态<br>\*   NEW：该文件没有写入过redo日志，新创建的redo文件或者文件被初始化<br>\*   INACTIVE：该状态下的redo文件包含的redo对应的页面均已写入磁盘。该状态下的redo文件可以被复用<br>\*   ACTIVE：该状态下的redo文件包含的redo对应的页面存在未写入磁盘的情况，或该redo暂未被归档。该状态下的文件不能被复用<br>\*   CURRENT：当前正在使用的redo文件 |
| TYPE | VARCHAR(8) | redo文件的类型<br>\*   ONLINE：表示为ONLINE文件，用于存储主库产生的redo<br>\*   STANDBY：表示为STANDBY文件，用于存储逻辑备库产生的redo文件 |
| ARCHIVED  | VARCHAR(3) | redo文件是否被归档<br>\*   YES：redo文件已经归档<br>\*   NO：redo文件未归档 |
| HEALTH | VARCHAR(32) | 数据库加载时redo文件的健康度<br>\*   NORMAL：redo文件正常<br>\*   MISSING：redo文件丢失<br>\*   HEAD_CURRUPTED：redo文件头部损坏 |
