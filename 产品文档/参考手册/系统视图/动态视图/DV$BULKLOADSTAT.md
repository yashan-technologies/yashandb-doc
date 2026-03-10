本视图显示分布式下列式存储稳态数据导入的内存信息。

| 字段                | 类型        | 说明                             |
| ------------------- | ----------- | -------------------------------- |
| GROUP_ID           | INTEGER     | 组ID                             |
| GROUP_NODE_ID     | INTEGER     | 组内节点ID                       |
| HANDLER_ID         | SMALLINT    | 实例ID                           |
| GLOBAL_SESSION_ID | INTEGER     | 分布式下全局会话ID               |
| TABLE_NAME         | VARCHAR(64) | 表名称                           |
| LOAD_START         | TIMESTAMP   | 导入开始时间                     |
| LOAD_TIME          | BIGINT      | 当前导入已执行时间               |
| ROW_NUM            | BIGINT      | 导入记录数                       |
| ROW_BYTES          | BIGINT      | 导入字节数                       |
| MEM_USE            | BIGINT      | 导入总内存使用量                 |
| MEM_QUOTA          | BIGINT      | 导入内存配额                     |
| SWAP_BYTES         | BIGINT      | 内存换入换出量                   |
| SWAP_TIME          | BIGINT      | 内存换入换出时间                 |
| WRITER_QUOTA       | BIGINT      | 冷数据writer使用内存配额        |
| BUFFER_QUOTA       | BIGINT      | 导入数据缓冲区使用内存配额       |
