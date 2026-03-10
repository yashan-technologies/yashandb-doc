本视图显示所有主节点上的LSC表的存储相关统计信息，存在分区时，按照分区划分并展示每个slice下每一列的信息。

| 字段                     | 类型       | 说明                               |
|------------------------|----------|----------------------------------|
| GROUP_ID               | NUMBER   | 组ID                              |
| GROUP_NODE_ID          | NUMBER   | 组内节点ID                           |
| INST_ID                | NUMBER   | 实例ID                             |
| BO                     | BIGINT   | 父表ID                             |
| OBJ                    | BIGINT   | 本表ID（分区ID）                       |
| DATAOBJ                | BIGINT   | 本表的数据对象ID                        |
| SLICE_ID              | BIGINT   | slice逻辑ID                        |
| COLUMN_ID             | SMALLINT | 列ID                              |
| COLUMN_FILE_SIZE     | BIGINT   | slice中该列的文件大小（包括数据文件和元数据文件）      |
| COLUMN_DATA_SIZE     | BIGINT   | slice中该列经过编码压缩后的最终数据大小（不包括元数据大小） |
| COLUMN_ENCODED_SIZE  | BIGINT   | slice中该列在编码后，压缩前的数据大小（不包括元数据大小）  |
| COLUMN_ORIGINAL_SIZE | BIGINT   | slice中该列对应的原始写入数据大小              |