本视图显示存算一体分布式集群中所有主节点上的LSC表的存储相关统计信息，存在分区时，按照分区划分并展示每个slice的信息。（不包括AC slice）

|  字段| 类型| 说明|
|------------------|----------|----------------------------------------------------------------|
| GROUP\_ID        | INTEGER  | 组ID                                                            |
| GROUP\_NODE\_ID  | INTEGER  | 组内节点ID                                                         |
| BO               | BIGINT   | LSC父表ID                                                        |
| OBJ              | BIGINT   | LSC子表ID（分区ID）                                                  |
| DATAOBJ          | BIGINT   | LSC子表的数据对象ID                                                   |
| SLICE\_ID        | BIGINT   | slice逻辑ID                                                      |
| STATUS           | INTEGER  | slice状态，0：invalid, 1：active, 2：full, 3：stale, 4：free, 5：stable |
| SORTED           | BOOLEAN  | 是否排序                                                           |
| COMPACTED        | BOOLEAN  | 是否合并                                                           |
| SCN              | BIGINT   | active slice变成full状态的scn，stable slice生成的scn                    |
| SPACE\_ID        | INTEGER  | slice所属表空间                                                     |
| BUCKET\_ID       | INTEGER  | slice所属data bucket，非stable slice该字段为NULL                       |
| SLICE\_FILE\_ID  | BIGINT   | slice的unique id，非stable slice该字段为NULL                          |
| FILE\_SIZE       | BIGINT   | slice文件大小（包括数据文件和元数据文件），非stable slice该字段为NULL                  |
| DATA\_SIZE       | BIGINT   | slice经过编码压缩后的最终数据大小（不包括元数据大小），非stable slice该字段为NULL            |
| ORIGINAL\_SIZE   | BIGINT   | slice对应的原始写入数据大小，非stable slice该字段为NULL                         |
| ROW\_COUNT       | BIGINT   | slice存储行数，非stable slice该字段为NULL                                |
| ROW\_GROUP\_NUM  | BIGINT   | slice中的rowgroup数量，非stable slice该字段为NULL                        |
