本视图显示切片文件归档索引信息，用于查看切片文件归档索引状态。

| 字段           | 类型         | 说明                     |
|----------------|--------------|--------------------------|
| DATA_OID       | BIGINT       | 切片文件归档的DATAOID               |
| SLICE_ID       | BIGINT       | 切片文件归档的ID                    |
| LFN            | BIGINT       | 切片文件归档的创建LFN                |
| BUCKET_ID      | INTEGER      | 切片文件归档中记录BUCKET的ID         |
| BUCKET_PATH    | VARCHAR(255) | 切片文件归档中记录BUCKET            |
| COLUMN_ID      | SMALLINT     | 切片文件归档的列ID                 |
| ARCH_INST_ID   | TINYINT      | 切片文件归档的创建实例ID            |
| HAS_CLEAN      | BOOLEAN      | 切片文件归档是否已清理              |
| SIZE           | BIGINT       | 切片文件归档大小                   |