本视图显示存算一体分布式集群中每个节点上磁盘缓存的状态信息。

|  字段| 类型| 说明|
|:----------------------|:----------|:---------------------------|
| GROUP_ID              | INTEGER   | 组ID                       |
| GROUP_NODE_ID         | INTEGER   | 组内节点ID                 |
| REGION_ID             | INTEGER   | 磁盘缓存的区域ID           |
| REGION_GROUP_ID       | INTEGER   | 磁盘缓存的区域所在组ID     |
| REGION_HIT            | INTEGER   | 磁盘缓存区域的总命中次数   |
| ITEM_NUM              | INTEGER   | 磁盘缓存区域的缓存对象数量 |
| REGION_LOAD_TIMESTAMP | TIMESTAMP(6) | 磁盘缓存区域开始加载时间戳 |
