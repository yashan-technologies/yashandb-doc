本视图显示分布式下列式存储稳态数据导入的内存信息。

|  字段| 类型| 说明|
| ------------------- | ----------- | -------------------------------- |
| GROUP\_ID           | INTEGER     | 组ID                             |
| GROUP\_NODE\_ID     | INTEGER     | 组内节点ID                       |
| HANDLER\_ID         | SMALLINT    | 实例ID                           |
| GLOBAL\_SESSION\_ID | INTEGER     | 分布式下全局会话ID               |
| TABLE\_NAME         | VARCHAR(64) | 表名称                           |
| LOAD\_START         | TIMESTAMP(6)   | 导入开始时间                     |
| LOAD\_TIME          | BIGINT      | 当前导入已执行时间               |
| ROW\_NUM            | BIGINT      | 导入记录数                       |
| ROW\_BYTES          | BIGINT      | 导入字节数                       |
| MEM\_USE            | BIGINT      | 导入总内存使用量                 |
| MEM\_QUOTA          | BIGINT      | 导入内存配额                     |
| SWAP\_BYTES         | BIGINT      | 内存换入换出量                   |
| SWAP\_TIME          | BIGINT      | 内存换入换出时间                 |
| WRITER\_QUOTA       | BIGINT      | 冷数据writer使用内存配额        |
| BUFFER\_QUOTA       | BIGINT      | 导入数据缓冲区使用内存配额       |
