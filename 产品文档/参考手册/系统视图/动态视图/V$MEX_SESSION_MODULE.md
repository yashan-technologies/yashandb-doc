本视图显示当前节点各个会话中，MEX内存池各个功能模块的使用情况。

| 字段                  | 类型          | 说明            |
|---------------------|-------------|---------------|
| SID                 | SMALLINT    | 会话ID          |
| TAG_ID             | INTEGER     | 模块标签ID        |
| NAME                | VARCHAR(64) | 模块标签名         |
| USING_SIZE         | BIGINT      | 使用中的内存（单位：字节） |
| TOTAL_ALLOC_TIMES | BIGINT      | 历史分配次数        |
| TOTAL_ALLOC_SIZE  | BIGINT      | 历史分配大小（单位：字节） |
| TOTAL_FREE_TIMES  | BIGINT      | 历史释放次数        |
| TOTAL_FREE_SIZE   | BIGINT      | 历史释放大小（单位：字节） |
