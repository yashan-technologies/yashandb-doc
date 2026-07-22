本视图显示存算一体分布式集群中所有节点归档gap区间。在主备网络不稳定，或备库长时间停机的情况下，备库会落后较多redo。为了加快redo同步速率，主库直接发送最新的redo给备库，中间缺失的redo会通过FAL线程发送归档日志来补齐，还没补齐的归档日志称为归档gap。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | 归档gap区间ID，如果gap是多个不连续的区间，将输出至少2行 |
| LOW\_SEQUENCE# | INTEGER | 当前归档gap区间的第一个序号（ASN） |
| HIGH\_SEQUENCE# | INTEGER | 当前归档gap区间的最后一个序号（ASN） |
