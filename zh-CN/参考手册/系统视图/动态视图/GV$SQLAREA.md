本视图显示共享SQL区中每条SQL的统计信息，包含SQL在statement上的内存消耗，解析，优化和执行信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SQL_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |
| SQL_FULLTEXT | CLOB | SQL CLOB形式的全字符 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| SHARABLE_MEM | INTEGER | 该SQL以及所有版本执行计划所占用的共享页面总和 |
| PERSISTENT_MEM | INTEGER | 该SQL以及所有版本执行计划实际使用的内存总和 |
| RUNTIME_MEM | INTEGER | 该SQL以及所有版本执行计划运行时所需要的从应用堆内存区中申请的内存总和 |
| VERSION_COUNT | INTEGER | 执行计划的版本数量 |
| LOADED_VERSIONS | INTEGER | 已经加载的执行计划版本数量 |
| OPEN_VERSIONS | INTEGER | 正在使用的执行计划的版本数量 |
| USERS_OPENING | INTEGER | 使用所有执行计划的用户数量 |
| USERS_EXECUTING | INTEGER | 正在执行所有计划的用户数量 |
| SORTS | BIGINT | 排序次数 |
| FETCHES | BIGINT | fetch次数 |
| EXECUTIONS | BIGINT | 执行次数 |
| PX_SERVERS_EXECUTIONS | BIGINT | 并行执行引擎的执行次数（保留字段） |
| END_OF_FETCH_COUNT | BIGINT | fetch到末尾的次数，如果当前还在fetch中或中途失败，则不会增加统计信息，因此其值应该小于等于EXECUTIONS字段值 |
| ROWS_PROCESSED | BIGINT | 处理的行数 |
| SERIALIZABLE_ABORTS | BIGINT | 事务未能序列化次数 |
| LOADS | BIGINT | 执行计划加载的次数（保留字段） |
| INVALIDATIONS | BIGINT | 执行计划失效的次数 |
| PARSE_CALLS | BIGINT | 解析的次数 |
| DISK_READS | BIGINT | 磁盘读取次数 |
| DIRECT_WRITES | BIGINT | 直接写次数 |
| DIRECT_READS | BIGINT | 直接读次数 |
| BUFFER_GETS | BIGINT | 从缓存区获取Buffer的次数 |
| IO_INTERCONNECT_BYTES | BIGINT | 数据库和存储系统之间的I/O交互次数 |
| PHYSICAL_READ_REQUESTS | BIGINT | 物理读请求次数 |
| PHYSICAL_READ_BYTES | BIGINT | 物理读的字节数 |
| PHYSICAL_WRITE_REQUESTS | BIGINT | 物理写的请求次数 |
| PHYSICAL_WRITE_BYTES | BIGINT | 物理写的字节数 |
| APPLICATION_WAIT_TIME | BIGINT | 应用等待时间 （单位：微秒） |
| CONCURRENCY_WAIT_TIME | BIGINT | 并发等待时间（单位：微秒） |
| CLUSTER_WAIT_TIME | BIGINT | 集群间的等待时间（保留字段）（单位：微秒） |
| USER_IO_WAIT_TIME | BIGINT | 用户I/O等待时间 （单位：微秒） |
| PLSQL_EXEC_TIME | BIGINT | PL执行时间（单位：微秒），保留字段 |
| CPU_TIME | BIGINT | 解析、执行、取数据的CPU时间 （单位：微秒）|
| ELAPSED_TIME | BIGINT | 解析、执行、取数据所经历的时间，如果是分布式集群，则包含DN侧的执行时间 （单位：微秒）|
| COMMAND_TYPE | INTEGER | SQL的命令类型<br/>\* 1：SQL_QUERY  <br/>\* 2：SQL_INSERT  <br/>\* 3：SQL_UPDATE  <br/>\* 4：SQL_DELETE  <br/>\* 5：SQL_MERGE  <br/>\* 6：SQL_WITH  <br/>\* 7：SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE | VARCHAR(10) | 保留字段 |
| OPTIMIZER_COST | BIGINT | 优化器给出的本次查询成本 |
| OPTIMIZER_ENV | RAW(2000) | 保留字段 |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT | 保留字段 |
| PARSING_USER_ID | INTEGER | 第一个解析该SQL的用户ID |
| PARSING_SCHEMA_ID | INTEGER | 解析该SQL时所用的schema ID |
| PARSING_SCHEMA_NAME | VARCHAR(64) | 解析该SQL时所用的schema名称 |
| ADDRESS | RAW(8) | SQL地址 |
| HASH_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| PLAN_HASH_VALUE | BIGINT | 一个执行计划的唯一标识 |
| MODULE | VARCHAR(64) | 保留字段 |
| MODULE_HASH | BIGINT | 保留字段 |
| ACTION | VARCHAR(64) | 保留字段 |
| ACTION_HASH | BIGINT | 保留字段 |
| OUTLINE_CATEGORY | VARCHAR(64) | 如果应用了一个outline，那么该字段为outline的类别，否则为NULL |
| OUTLINE_SID | INTEGER | 根据该字段可以确定该计划是使用public outline（该字段为NULL）还是private outline（该字段为相应的session sid） |
| LAST_ACTIVE_CHILD_ADDRESS | RAW(8) | 上一次使用的执行计划的地址 |
| LITERAL_HASH_VALUE | BIGINT | 保留字段 |
| FIRST_LOAD_TIME | DATE | 执行计划第一次加载的时间 |
| LAST_LOAD_TIME | DATE | 上一次加载时间 |
| LAST_ACTIVE_TIME | DATE | 上一次执行计划被激活的时间 |
| IS_OBSOLETE | VARCHAR(1) | 是否已经过时，如果执行计划版本太多或者太大，则有可能过时 |
| IS_BIND_SENSITIVE | VARCHAR(1) | 是否对绑定参数敏感的，如果绑定参数值发生变化，需要重新优化 |
| BIND_DATA | RAW(2000) | 保留字段 |
| LOCKED_TOTAL | BIGINT | 执行计划被锁次数，保留字段 |
| PINNED_TOTAL | BIGINT | 执行计划被暂留住的次数 |
| IS_REOPTIMIZABLE | VARCHAR(1) | 是否需要重新优化 |
| RESTART_STATEMENTS | BIGINT | statement重启次数 |
| FIRST_LOAD_LOCATION | BIGINT | SQL地址信息 |
| BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的最新页面的次数 |
| CR_BLOCK_RECEIVED | BIGINT | 集群下从其他节点获取的CR页面的次数 |
| LOCAL_GRANTS | BIGINT | 集群下本节点授权加载页面的次数 |
| REMOTE_GRANTS | BIGINT | 集群下其他节点授权加载页面的次数 |
| LOCAL_UPGRADES | BIGINT | 集群下本节点授权页面锁升级的次数 |
| REMOTE_UPGRADES | BIGINT | 集群下其他授权页面锁升级的次数 |
