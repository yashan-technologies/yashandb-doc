本视图显示所有节点MEX内存池各个功能模块的信息。

|  字段| 类型| 说明|
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | 组ID           |
| GROUP_NODE_ID       | NUMBER      | 组内节点ID        |
| INST_ID             | NUMBER      | 实例ID          |
| TAG_ID             | INTEGER     | 模块标签ID        |
| NAME                | VARCHAR(64) | 模块标签名         |
| TOTAL_ALLOC_TIMES | BIGINT      | 历史分配次数        |
| TOTAL_ALLOC_SIZE  | BIGINT      | 历史分配大小（单位：字节） |
| TOTAL_FREE_TIMES  | BIGINT      | 历史释放次数        |
| TOTAL_FREE_SIZE   | BIGINT      | 历史释放大小（单位：字节） |
