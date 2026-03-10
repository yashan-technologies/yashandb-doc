本视图显示子游标的详细执行计划信息，需要配置参数statistics_level=all才能使用。

|  字段| 类型| 说明|
| --- | --- | --- |
| ADDRESS | RAW(8) | SQL地址 |
| HASH_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| PLAN_HASH_VALUE | BIGINT | 等同于SQL_ID |
| CHILD_ADDRESS | RAW(8) | 子游标地址 |
| CHILD_NUMBER | INTEGER | 子游标编号 |
| OPERATION_ID | BIGINT | 执行计划中每个步骤的编号（保留字段） |
| EXECUTIONS | BIGINT | 该游标被执行的次数 |
| LAST_STARTS | BIGINT | 在上次执行期间，此操作已启动的次数 |
| STARTS | BIGINT | 此操作已启动的次数，累积在过去的执行中 |
| LAST_OUTPUT_ROWS | BIGINT | 行源在上次执行期间生成的行数 |
| OUTPUT_ROWS | BIGINT | 行源生成的行数，在过去的执行中累积 |
| LAST_CR_BUFFER_GETS | BIGINT | 上次执行期间从缓冲区读取的一致性块的数量 |
| CR_BUFFER_GETS | BIGINT | 从缓冲区读取的一致性块的数量，在过去的执行中累积 |
| LAST_CU_BUFFER_GETS | BIGINT | 上次执行期间从缓冲区读取的当前模式块的数量。在当前模式下为 INSERT、UPDATE 和 DELETE 等语句检索缓冲区 |
| CU_BUFFER_GETS | BIGINT | 从缓冲区读取的当前模式块的数量，在过去的执行中累积。在当前模式下为 INSERT、UPDATE 和 DELETE 等语句检索缓冲区 |
| LAST_DISK_READS | BIGINT | 操作执行的物理磁盘读取次数，在上次执行期间 |
| DISK_READS | BIGINT | 操作执行的物理磁盘读取数，在过去的执行中累积 |
| LAST_DISK_WRITES | BIGINT | 操作执行的物理磁盘写入次数，在上次执行期间 |
| DISK_WRITES | BIGINT | 操作执行的物理磁盘写入次数，在过去的执行中累积 |
| LAST_ELAPSED_TIME | BIGINT | 与此操作对应的经过时间（单位：微秒），在上次执行期间 |
| ELAPSED_TIME | BIGINT | 与此操作相对应的经过时间（单位：微秒），在过去的执行中累积 |
| BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的最新页面的次数 |
| CR_BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的CR页面的次数 |
| LOCAL_GRANTS | BIGINT | 集群下本节点授权加载页面的次数 |
| REMOTE_GRANTS | BIGINT | 集群下其他节点授权加载页面的次数 |
| LOCAL_UPGRADES | BIGINT | 集群下本节点授权页面锁升级的次数 |
| REMOTE_UPGRADES | BIGINT | 集群下其他授权页面锁升级的次数 |
