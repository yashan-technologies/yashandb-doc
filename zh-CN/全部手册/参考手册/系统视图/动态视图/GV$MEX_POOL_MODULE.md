本视图显示所有节点内存扩展池各个功能模块的信息。

|  字段| 类型| 说明|
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | 组ID           |
| GROUP_NODE_ID       | NUMBER      | 组内节点ID        |
| INST_ID             | NUMBER      | 实例ID          |
| TAG_ID             | INTEGER     | 模块标签ID        |
| NAME                | VARCHAR(64) | 内存组件的名称<br/>* APP MEM INIT：会话私有的堆内存  <br/>* APP MEM EXTEND：全局应用池  <br/>* PQ POOL：并行查询池   <br/>* POOL META：内存扩展池元数据  <br/>* OTHERS：SQL批量执行和JSON可用的内存          |
| TOTAL_ALLOC_TIMES | BIGINT      | 历史分配次数        |
| TOTAL_ALLOC_SIZE  | BIGINT      | 历史分配大小（单位：字节） |
| TOTAL_FREE_TIMES  | BIGINT      | 历史释放次数        |
| TOTAL_FREE_SIZE   | BIGINT      | 历史释放大小（单位：字节） |
