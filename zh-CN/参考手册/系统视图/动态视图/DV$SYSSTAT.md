本视图显示存算一体分布式集群中所有节点所有会话的相关统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| STATISTIC# | INTEGER | 统计的编号 |
| NAME | VARCHAR(64) | 系统统计项名称，可以通过查询V$SYSSTAT视图获得完整的统计项名称 |
| CLASS | INTEGER | 系统统计项类别<br>\*   1：用户<br>\*   2：redo<br>\*   4：enqueue<br>\*   8：cache<br>\*   16：OS<br>\*   32：cluster<br>\*   64：SQL<br>\*   128：DEBUG |
| VALUE | BIGINT | 统计值 |
