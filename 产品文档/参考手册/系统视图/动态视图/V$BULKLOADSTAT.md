本视图显示列式存储稳态数据导入的内存信息。

| 字段          | 类型        | 说明                             |
| ------------- | ----------- | -------------------------------- |
| HANDLER_ID   | SMALLINT    | 实例ID                           |
| TABLE_NAME   | VARCHAR(64) | 表名称                           |
| LOAD_START   | TIMESTAMP   | 导入开始时间                     |
| LOAD_TIME    | BIGINT      | 当前导入已执行时间               |
| ROW_NUM      | BIGINT      | 导入记录数                       |
| ROW_BYTES    | BIGINT      | 导入字节数                       |
| MEM_USE      | BIGINT      | 导入总内存使用量                 |
| MEM_QUOTA    | BIGINT      | 导入内存配额                     |
| SWAP_BYTES   | BIGINT      | 内存换入换出量                   |
| SWAP_TIME    | BIGINT      | 内存换入换出时间                 |
| WRITER_QUOTA | BIGINT      | 导入写入冷数据writer使用内存配额 |
| BUFFER_QUOTA | BIGINT      | 导入数据缓冲区使用内存配额       |