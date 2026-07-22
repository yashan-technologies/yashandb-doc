本视图显示字典缓存上列的统计信息内容。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| OBJ# | BIGINT | 列所在的表对象或分区对象的ID |
| COL# | INTEGER | 列ID |
| BUCKET\_COUNT | BIGINT | 直方图的bucket数量 |
| ROW\_COUNT | INTEGER | 直方图的行数 |
| NULL\_COUNT | BIGINT | 列的空行数 |
| MINIMUM | BIGINT | bucket数量最小值 |
| MAXIMUM | BIGINT | bucket数量最大值 |
| DIST\_COUNT | BIGINT | 列的distinct数量 |
| LOWVAL | RAW(1000) | 列的最小值 |
| HIGHVAL | RAW(1000) | 列的最大值 |
| DENSITY | FLOAT | 列的密度 |
| AVG\_LENGTH | INTEGER | 列的平均长度 |
| MAX\_LENGTH | INTEGER | 列的最大长度 |
| FLAGS | INTEGER | statistics的属性<br>\*   0X01：标识statistics是否是由用户直接指定<br>\*   0X02：标识分区索引的statistics是全量统计得到还是由分区估算得到<br>\*   0X04：标识statistics是否被收集<br>\*   0X100：statistics是否被锁定 |
| TYPE | INTEGER | 直方图类型 |
| ANALYZE\_TIME | DATE | 上次收集统计信息的时间 |
| SAMPLE\_SIZE | BIGINT | 统计信息采样的行数 |
| BLOCK\_COUNT | BIGINT | 列的数据块数量 |
