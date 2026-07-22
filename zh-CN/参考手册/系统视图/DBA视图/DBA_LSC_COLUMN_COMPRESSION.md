本视图显示所有LSC表（分区）下列数据的压缩信息。

|  字段| 类型| 说明|
|--------------------------|-------------|----------------------------|
| OWNER                    | VARCHAR(64) | LSC表所属用户名                  |
| TABLESPACE\_NAME         | VARCHAR(64) | LSC所在表空间名                  |
| TABLE\_NAME              | VARCHAR(64) | LSC表名                      |
| SUB\_NAME                | VARCHAR(64) | 如果是分区表，表示分区表名              |
| COLUMN\_ID               | SMALLINT    | 列ID                        |
| ORIGINAL\_SIZE           | NUMBER      | 原始输入数据大小                   |
| DATA\_ENCODED\_SIZE      | NUMBER      | 编码后压缩前的数据大小（不包括元数据）        |
| DATA\_COMPRESSED\_SIZE   | NUMBER      | 编码压缩后的数据文件大小（不包括元数据文件）     |
| META\_COMPRESSED\_SIZE   | NUMBER      | 编码压缩后的元数据文件大小（不包括数据文件）     |
| COMPRESSED\_SIZE         | NUMBER      | 编码压缩后的数据大小（包括数据文件和元数据文件)   |
| DATA\_COMPRESSION\_RATIO | NUMBER      | 数据文件的编码压缩百分比（不包括元数据文件）     |
| COMPRESSION\_RATIO       | NUMBER      | 所有数据的编码压缩百分比（包括数据文件和元数据文件) |
