本视图显示节点自身的节点信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| TYPE | VARCHAR(16) | 节点类型，例如CN、DN等 |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ENDPOINT | SMALLINT | 内部网络通信端点 |
| TERM | BIGINT | 节点任期 |
| ROLE | VARCHAR(16) | 节点角色，例如Primary、Standby |
| VERSION | INTEGER | 节点静态信息版本号 |
| STATE | VARCHAR(16) | 节点静态状态，例如INVALID（无效值）、INIT（初始化）、REMOVED（删除）、STARTED（启动）、STOPPED（停止）、ISOLATED（隔离）|
| STATUS_VERSION | BIGINT | 节点动态信息版本号 |
| RUNNING_STATE | VARCHAR(32) | 节点动态状态，例如INVALID（无效值）、NORMAL（正常）、SUSPECT（猜测）、ABNORMAL（异常）、FULL SYNC（同步）|
| HOST | VARCHAR(256) | 节点服务器地址 |
| DATA_PATH | VARCHAR(256) | 节点数据存放目录 |
| LISTEN_ADDR | VARCHAR(128) | 节点服务地址 |
| REPLICA_ADDR | VARCHAR(128) | 节点复制链路地址 |
| DIN_ADDR | VARCHAR(128) | 分布式内部通信地址 |
| CREATE_TIME | TIMESTAMP | 节点创建时间 |
| LAST_UPDATE_TIME | TIMESTAMP | 节点静态信息最后更新时间 |
| STATUS_UPDATE_TIME | TIMESTAMP | 节点动态信息最后更新时间 |