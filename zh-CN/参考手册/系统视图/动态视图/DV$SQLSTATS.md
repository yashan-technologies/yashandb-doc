本视图显示存算一体分布式集群中所有节点的SQL执行计划统计信息汇总。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| SQL\_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |
| SQL\_FULLTEXT | CLOB | SQL CLOB形式的全字符 |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| LAST\_ACTIVE\_TIME | DATE | 上一次访问该SQL的时间 |
| LAST\_ACTIVE\_CHILD\_ADDRESS | RAW(8) | 保留字段 |
| PLAN\_HASH\_VALUE | BIGINT | 一个执行计划的唯一标识 |
| PARSE\_CALLS | BIGINT | 解析调用次数 |
| DISK\_READS | BIGINT | 读磁盘次数 |
| DIRECT\_WRITES | BIGINT | 游标直接写的次数 |
| BUFFER\_GETS | BIGINT | 读缓存区次数 |
| ROWS\_PROCESSED | BIGINT | SQL语句返回的总列数 |
| SERIALIZABLE\_ABORTS | BIGINT | 事务未能序列化次数 |
| FETCHES | BIGINT | SQL语句的fetch次数 |
| EXECUTIONS | BIGINT | 被载入缓存库后的执行次数 |
| END\_OF\_FETCH\_COUNT | BIGINT | 光标被带到库缓存中后，该光标完全执行的次数 |
| LOADS | BIGINT | SQL进入SQL缓存池的次数 |
| VERSION\_COUNT | INTEGER | 执行计划的版本数量 |
| INVALIDATIONS | BIGINT | 执行计划失效的次数 |
| PX\_SERVERS\_EXECUTIONS | BIGINT | 以并行方式执行的总次数 |
| CPU\_TIME | BIGINT | 解析/执行/取得等CPU使用时间 （单位：微秒） |
| ELAPSED\_TIME | BIGINT | 解析/执行/取得等消耗时间（单位：微秒） |
| APPLICATION\_WAIT\_TIME | BIGINT | 应用等待时间（单位：微秒） |
| CONCURRENCY\_WAIT\_TIME | BIGINT | 并发等待时间（单位：微秒） |
| CLUSTER\_WAIT\_TIME | BIGINT | 集群等待时间（保留字段）（单位：微秒） |
| USER\_IO\_WAIT\_TIME | BIGINT | 用户I / O等待时间（单位：微秒） |
| PLSQL\_EXEC\_TIME | BIGINT | PL执行时间（单位：微秒），保留字段 |
| JAVA\_EXEC\_TIME | BIGINT | 保留字段 |
| SORTS | BIGINT | 完成的排序数 |
| SHARABLE\_MEM | INTEGER | 在SQL缓存池中占用的共享内存大小（单位：字节） |
| TOTAL\_SHARABLE\_MEM | INTEGER | 在SQL缓存池中总共占用的共享内存大小（单位：字节），等同于SHARABLE\_MEM |
