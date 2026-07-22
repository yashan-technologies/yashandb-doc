本视图显示归档切片文件索引信息，用于在集群环境下查看所有实例的归档切片文件索引状态。

| 字段           | 类型         | 说明         |
|----------------|--------------|--------------|
| GROUP_ID       | NUMBER       | 组ID         |
| GROUP_NODE_ID  | NUMBER       | 组内节点ID   |
| INST_ID        | NUMBER       | 实例ID       |
| DATA_OID       | BIGINT       | 归档切片文件的DATAOID               |
| SLICE_ID       | BIGINT       | 归档切片文件的ID                   |
| LFN            | BIGINT       | 归档切片文件的创建LFN               |
| BUCKET_ID      | INTEGER      | 归档切片文件中记录BUCKET的ID                |
| BUCKET_PATH    | VARCHAR(255) | 归档切片文件中记录BUCKET          |
| COLUMN_ID      | SMALLINT     | 归档切片文件的列ID                     |
| ARCH_INST_ID   | TINYINT      | 归档切片文件的创建实例ID               |
| HAS_CLEAN      | BOOLEAN      | 归档切片文件是否已清理               |
| SIZE           | BIGINT       | 归档切片文件大小         |