本视图显示所有节点各个会话中内存扩展池各个功能模块的使用情况。

|  字段| 类型| 说明|
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | 组ID           |
| GROUP_NODE_ID       | NUMBER      | 组内节点ID        |
| INST_ID             | NUMBER      | 实例ID          |
| SID                 | SMALLINT    | 会话ID          |
| TAG\_ID             | INTEGER     | 内存组件的ID <br/>* 1：会话私有的应用池  <br/>* 2：全局应用池  <br/>* 3：并行查询池   <br/>* 4：内存扩展池元数据  <br/>* 0：SQL批量执行和JSON可用的内存           |
| NAME                | VARCHAR(64) | 内存组件的名称<br/>* APP MEM INIT：会话私有的堆内存  <br/>* APP MEM EXTEND：全局应用池  <br/>* PQ POOL：并行查询池   <br/>* POOL META：内存扩展池元数据  <br/>* OTHERS：SQL批量执行和JSON可用的内存           |
| USING\_SIZE         | BIGINT      | 使用中的内存（单位：字节） |
| TOTAL\_ALLOC\_TIMES | BIGINT      | 历史分配次数        |
| TOTAL\_ALLOC\_SIZE  | BIGINT      | 历史分配大小（单位：字节） |
| TOTAL\_FREE\_TIMES  | BIGINT      | 历史释放次数        |
| TOTAL\_FREE\_SIZE   | BIGINT      | 历史释放大小（单位：字节） |
