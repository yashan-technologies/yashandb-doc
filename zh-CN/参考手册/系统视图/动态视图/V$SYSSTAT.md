本视图用于展示系统统计项信息，包含了当前实例上所有会话的统计项信息之和。

|  字段| 类型| 说明|
| --- | --- | --- |
| STATISTIC# | INTEGER | 统计项ID |
| NAME | VARCHAR(64) | 系统统计项名称 |
| CLASS | INTEGER | 系统统计项类别<br>\*   1：用户<br>\*   2：redo<br>\*   4：enqueue<br>\*   8：cache<br>\*   16：OS<br>\*   32：cluster<br>\*   64：SQL<br>\*   128：DEBUG |
| VALUE | BIGINT | 统计值 |
