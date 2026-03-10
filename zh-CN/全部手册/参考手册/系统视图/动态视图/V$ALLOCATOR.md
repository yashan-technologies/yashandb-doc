本视图显示当前使用内存的状况。

|  字段| 类型| 说明|
| --- | --- | --- |
| NAME | VARCHAR(64) | 内存类型 |
| TOTAL_MEMORY | BIGINT | 分配的内存总大小（单位：字节） |
| CURR_MEMORY_USED | BIGINT | 当前使用的内存总大小（单位：字节） |
| FREE_MEMORY | BIGINT | 当前剩余的内存大小（单位：字节） |
| MAX_MEMORY_USED | BIGINT | 节点本次运行期间使用过的内存峰值（单位：字节） |
