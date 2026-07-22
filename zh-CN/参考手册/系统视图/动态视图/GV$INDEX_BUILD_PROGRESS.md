本视图显示节点内所有索引的构建进度信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| TABLE_NAME | VARCHAR(64) | 表名 |
| INDEX_NAME | VARCHAR(64) | 索引名 |
| INDEX_TYPE | VARCHAR(64) | 索引类型 |
| PROGRESS | INTEGER | 构建进度（0到100） |
| TIME_USED | BIGINT | 已耗时间（单位：毫秒） |
| TIME_REMAINING | BIGINT | 剩余时间（单位：毫秒） |
| BUILD_MESSAGE | VARCHAR(256) | 构建信息 |
