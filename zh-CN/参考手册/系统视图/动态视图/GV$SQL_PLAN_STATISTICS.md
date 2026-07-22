本视图显示子游标的详细执行计划信息，需要配置参数statistics\_level=all才能使用。保留视图，仅作兼容，无实际数据。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ADDRESS | RAW(8) | SQL地址 |
| HASH\_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| PLAN\_HASH\_VALUE | BIGINT | 等同于SQL\_ID |
| CHILD\_ADDRESS | RAW(8) | 子游标地址 |
| CHILD\_NUMBER | INTEGER | 子游标编号 |
| OPERATION\_ID | BIGINT | 执行计划中每个步骤的编号（保留字段） |
| EXECUTIONS | BIGINT | 该游标被执行的次数 |
| LAST\_STARTS | BIGINT | 在上次执行期间，此操作已启动的次数 |
| STARTS | BIGINT | 此操作已启动的次数，累积在过去的执行中 |
| LAST\_OUTPUT\_ROWS | BIGINT | 行源在上次执行期间生成的行数 |
| OUTPUT\_ROWS | BIGINT | 行源生成的行数，在过去的执行中累积 |
| LAST\_CR\_BUFFER\_GETS | BIGINT | 上次执行期间从缓冲区读取的一致性块的数量 |
| CR\_BUFFER\_GETS | BIGINT | 从缓冲区读取的一致性块的数量，在过去的执行中累积 |
| LAST\_CU\_BUFFER\_GETS | BIGINT | 上次执行期间从缓冲区读取的当前模式块的数量。在当前模式下为 INSERT、UPDATE 和 DELETE 等语句检索缓冲区 |
| CU\_BUFFER\_GETS | BIGINT | 从缓冲区读取的当前模式块的数量，在过去的执行中累积。在当前模式下为 INSERT、UPDATE 和 DELETE 等语句检索缓冲区 |
| LAST\_DISK\_READS | BIGINT | 操作执行的物理磁盘读取次数，在上次执行期间 |
| DISK\_READS | BIGINT | 操作执行的物理磁盘读取数，在过去的执行中累积 |
| LAST\_DISK\_WRITES | BIGINT | 操作执行的物理磁盘写入次数，在上次执行期间 |
| DISK\_WRITES | BIGINT | 操作执行的物理磁盘写入次数，在过去的执行中累积 |
| LAST\_ELAPSED\_TIME | BIGINT | 与此操作对应的经过时间（单位：毫秒），在上次执行期间 |
| ELAPSED\_TIME | BIGINT | 与此操作相对应的经过时间（单位：毫秒），在过去的执行中累积 |
| BLOCK\_RECEIVED | BIGINT | 集群下从其他节点获取的最新页面的次数 |
| CR\_BLOCK\_RECEIVED | BIGINT | 集群下从其他节点获取的CR页面的次数 |
| LOCAL\_GRANTS | BIGINT | 集群下本节点授权加载页面的次数 |
| REMOTE\_GRANTS | BIGINT | 集群下其他节点授权加载页面的次数 |
| LOCAL\_UPGRADES | BIGINT | 集群下本节点授权页面锁升级的次数 |
| REMOTE\_UPGRADES | BIGINT | 集群下其他授权页面锁升级的次数 |
