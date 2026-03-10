本视图显示全局资源情况。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| DATA_OBJECT_ID  | BIGINT     | 亲和策略对应的data object id                                 |
| POLICY          | VARCHAR(8) | 亲和策略： <br/> * DEFAULT为默认策略，即按GHT分布 <br/> * AFFINITY，指定实例亲和 <br/> * AUTO，自动亲和，目前为预留类型 |
| MASTER          | SMALLINT   | 亲和实例                                                     |
| CURRENT_MASTER  | SMALLINT   | obj当前所在的实例（当亲和实例不在线时，会被托管至其他在线实例） |
| PREVIOUS_MASTER | SMALLINT   | 上一次的亲和实例                                             |
| STATUS          | SMALLINT   | 当前资源迁移状态                                             |
