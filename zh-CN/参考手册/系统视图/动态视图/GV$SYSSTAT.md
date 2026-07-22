本视图显示所有session的相关统计信息。

|  字段| 类型| 说明|
|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------|
| GROUP_ID      | NUMBER      | 组ID |
| GROUP_NODE_ID | NUMBER      | 组内节点ID |
| INST_ID       | NUMBER      | 实例ID                                                                                                                                      |
| STATISTIC#    | INTEGER     | 统计项ID                                                                                                                                 |
| NAME          | VARCHAR(64) | 系统统计项名称                                                                                                                               |
| CLASS         | INTEGER     | 系统统计项类别<br>\*   1：用户<br>\*   2：redo<br>\*   4：enqueue<br>\*   8：cache<br>\*   16：OS<br>\*   32：cluster<br>\*   64：SQL<br>\*   128：DEBUG |
| VALUE         | BIGINT      | 统计值                                                                                                                                   |
