本视图显示SQL游标的基本性能统计信息，每一行代表SQL文本和优化器计划的唯一组合（即SQL_ID和 PLAN_HASH_VALUE 的唯一组合）的数据。

保留字段即暂为空值。

|  字段| 类型| 说明|
| --- | --- | --- |
| SQL_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |
| SQL_FULLTEXT | CLOB | SQL CLOB形式的全字符 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| LAST_ACTIVE_TIME | DATE | 上一次访问该SQL的时间 |
| LAST_ACTIVE_CHILD_ADDRESS | RAW(8) | 保留字段 |
| PLAN_HASH_VALUE | BIGINT | 一个执行计划的唯一标识 |
| PARSE_CALLS | BIGINT | 解析调用次数 |
| DISK_READS | BIGINT | 读磁盘次数 |
| DIRECT_WRITES | BIGINT | 游标直接写的次数 |
| BUFFER_GETS | BIGINT | 读缓存区次数 |
| ROWS_PROCESSED | BIGINT | SQL语句返回的总列数 |
| SERIALIZABLE_ABORTS | BIGINT | 事务未能序列化次数 |
| FETCHES | BIGINT | SQL语句的fetch次数 |
| EXECUTIONS | BIGINT | 被载入缓存库后的执行次数 |
| END_OF_FETCH_COUNT | BIGINT | 光标被带到库缓存中后，该光标完全执行的次数 |
| LOADS | BIGINT | SQL进入SQL缓存池的次数 |
| VERSION_COUNT | INTEGER | 执行计划的版本数量 |
| INVALIDATIONS | BIGINT | 执行计划失效的次数 |
| PX_SERVERS_EXECUTIONS | BIGINT | 以并行方式执行的总次数 |
| CPU_TIME | BIGINT | 解析/执行/取得等CPU使用时间（单位：微秒） |
| ELAPSED_TIME | BIGINT | 解析/执行/取得等消耗时间（单位：微秒） |
| APPLICATION_WAIT_TIME | BIGINT | 应用等待时间（单位：微秒） |
| CONCURRENCY_WAIT_TIME | BIGINT | 并发等待时间（单位：微秒） |
| CLUSTER_WAIT_TIME | BIGINT | 集群等待时间（保留字段）（单位：微秒） |
| USER_IO_WAIT_TIME | BIGINT | 用户I / O等待时间 （单位：微秒） |
| PLSQL_EXEC_TIME | BIGINT | PL执行时间（单位：微秒），保留字段 |
| JAVA_EXEC_TIME | BIGINT | 保留字段 |
| SORTS | BIGINT | 完成的排序数 |
| SHARABLE_MEM | INTEGER | 在SQL缓存池中占用的共享内存大小（单位：字节） |
| TOTAL_SHARABLE_MEM | INTEGER | 在SQL缓存池中总共占用的共享内存大小（单位：字节），等同于SHARABLE_MEM |
| BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的最新页面的次数 |
| CR_BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的CR页面的次数 |
| LOCAL_GRANTS | BIGINT | 集群下本节点授权加载页面的次数 |
| REMOTE_GRANTS | BIGINT | 集群下其他节点授权加载页面的次数 |
| LOCAL_UPGRADES | BIGINT | 集群下本节点授权页面锁升级的次数 |
| REMOTE_UPGRADES | BIGINT | 集群下其他授权页面锁升级的次数 |
