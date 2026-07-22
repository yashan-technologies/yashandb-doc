本视图显示字典缓存上表的统计信息内容 。

|  字段| 类型| 描述|
| --- | --- | --- |
| OBJ# | BIGINT | 表对象ID |
| PART# | BIGINT | 表分区ID，若表对象没有分区，则该字段为NULL |
| FLAGS | INTEGER | 表的标识<br>\*   0X01：是否分布表<br>\*   0X02：是否复制表<br>\*   0X4：statistics是否由用户直接指定<br>\*   0X8：标识分区表的statistics是全量统计得到还是由分区估算得到 |
| ROW\_COUNT | BIGINT | 表的行数 |
| BLOCK\_COUNT | BIGINT | 表的数据块数量 |
| EMPTY\_COUNT | BIGINT | 空数据块数量 |
| CHAIN\_COUNT | BIGINT | 行链接数量 |
| AVG\_ROW\_SIZE | INTEGER | 平均行长度 |
| AVG\_SPACE | INTEGER | 平均空间使用率 |
| ANALYZE\_TIME | DATE | 上次收集统计信息的时间 |
| SAMPLESIZE | BIGINT | 统计信息采样的行数 |
