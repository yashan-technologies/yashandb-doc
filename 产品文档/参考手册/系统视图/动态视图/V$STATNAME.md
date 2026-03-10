本视图显示统计项的信息，与V$SYSSTAT中统计项对应。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| STATISTIC# | INTEGER | 统计项ID |
| NAME | VARCHAR(64) | 系统统计项名称 |
| CLASS | INTEGER | 系统统计项类别<br>\*   1：用户<br>\*   2：redo<br>\*   4：enqueue<br>\*   8：cache<br>\*   16：OS<br>\*   32：cluster<br>\*   64：SQL<br>\*   128：DEBUG |

