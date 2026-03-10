本视图显示所有LSC表（分区）的压缩信息。

| 字段                       | 类型          | 说明                         |
|--------------------------|-------------|----------------------------|
| OWNER                    | VARCHAR(64) | LSC表所属用户名                  |
| TABLESPACE_NAME         | VARCHAR(64) | LSC所在表空间名                  |
| TABLE_NAME              | VARCHAR(64) | LSC表名                      |
| SUB_NAME                | VARCHAR(64) | 如果是分区表，表示分区表名              |
| ROW_COUNT               | NUMBER      | 数据行数                       |
| ORIGINAL_SIZE           | NUMBER      | 原始输入数据大小                   |
| DATA_ENCODED_SIZE      | NUMBER      | 编码后压缩前的数据大小（不包括元数据）        |
| DATA_COMPRESSED_SIZE   | NUMBER      | 编码压缩后的数据文件大小（不包括元数据文件）     |
| META_COMPRESSED_SIZE   | NUMBER      | 编码压缩后的元数据文件大小（不包括数据文件）     |
| COMPRESSED_SIZE         | NUMBER      | 编码压缩后的数据大小（包括数据文件和元数据文件)   |
| DATA_COMPRESSION_RATIO | NUMBER      | 数据文件的编码压缩百分比（不包括元数据文件）     |
| COMPRESSION_RATIO       | NUMBER      | 所有数据的编码压缩百分比（包括数据文件和元数据文件) |

