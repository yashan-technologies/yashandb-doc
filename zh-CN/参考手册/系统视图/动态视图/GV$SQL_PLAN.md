本视图显示所有的执行计划信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ADDRESS | RAW(8) | SQL地址                                  |
| HASH\_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到                    |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| PLAN\_HASH\_VALUE | BIGINT | 一个执行计划的唯一标识                                  |
| CHILD\_ADDRESS | RAW(8) | 子游标地址                                  |
| CHILD\_NUMBER | INTEGER | 子游标编号             |
| TIMESTAMP | DATE | 执行计划被创建的时间                            |
| OPERATION | VARCHAR(256) | 执行计划explain之后的描述信息                    |
| OPTIONS | VARCHAR(256) | 执行计划explain之后与operation一起的描述信息        |
| OBJECT\_NODE | VARCHAR(256) | 表或视图的表述信息                             |
| OBJECT# | BIGINT | 表的ID                                  |
| OBJECT\_OWNER | VARCHAR(64) | 表或索引的所属者用户名                           |
| OBJECT\_NAME | VARCHAR(64) | 表或者索引名称                               |
| OBJECT\_ALIAS | VARCHAR(256) | 表的别名                                  |
| OBJECT\_TYPE | VARCHAR(64) | 对象的类型<br/>* TABLE FUNCTION<br/>* TABLE |
| OPTIMIZER | VARCHAR(64) | 保留字段                                  |
|PARTITION\_INFO|VARCHAR(64)| 执行计划的分区信息                             |
| ID  | INTEGER | 每个执行计划的ID                             |
| PARENT\_ID | INTEGER | 下个执行计划的ID                             |
| DEPTH | INTEGER | 当前执行计划在整个计划树中的深度（root节点深度为0）          |
| POSITION | INTEGER | 具有相同PARENT\_ID的所有操作的处理顺序              |
| SEARCH\_COLUMNS | BIGINT | 具有start和stop键的索引列的数目（即具有匹配谓词的列的数目）    |
| COST | BIGINT | 基于成本方法估计的操作成本                         |
| CARDINALITY | BIGINT | 根据优化器基于成本的方法估计操作产生的行数                 |
| BYTES | BIGINT | 根据优化器基于成本的方法估计操作产生的字节数                |
| OTHER\_TAG | VARCHAR(256) | 描述other列的内容                           |
| PARTITION\_START | INTEGER | 启动被访问的一系列分区                           |
| PARTITION\_STOP | INTEGER | 停止被访问的一系列分区                           |
| PARTITION\_ID | INTEGER | 分区ID                                  |
| OTHER | VARCHAR(4000) | 描述other列的内容                           |
| CPU\_COST | BIGINT | 根据优化器基于成本的方法估计操作会产生的CPU消耗             |
| IO\_COST | BIGINT | 根据优化器基于成本的方法估计操作会产生的IO成本              |
| TEMP\_SPACE | BIGINT | 根据优化器基于成本的方法估计的临时空间使用情况               |
| ACCESS\_PREDICATES | VARCHAR(4000) | 用于定位访问行数据的谓词                          |
| FILTER\_PREDICATES | VARCHAR(4000) | 用于在生成行之前筛选行的谓词                        |
| PROJECTION | VARCHAR(4000) | 保留字段                                  |
| TIME | BIGINT | 根据优化器基于成本的方法估计的操作运行时间（单位：ms毫秒）       |
| QBLOCK\_NAME | VARCHAR(64) | 每个查询块的名称                              |
| REMARKS | VARCHAR(4000) | 备注                                    |
