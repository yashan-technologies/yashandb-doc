本视图显示存算一体分布式集群中所有节点的SQL执行统计信息，同一个CN执行同一个SQL显示一条，不同CN执行同一个SQL显示多条。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SQL_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |
| SQL_FULLTEXT | CLOB | SQL CLOB形式的全字符 |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| SHARABLE_MEM | INTEGER | 在SQL缓存池中占用的共享内存大小（单位：字节） |
| PERSISTENT_MEM | INTEGER | 在SQL缓存池中占用的页面内存减去尾部未使用的内存（单位：字节） |
| RUNTIME_MEM | INTEGER | 此SQL在执行过程中申请的内存（单位：字节） |
| LOADED_VERSIONS | INTEGER | 显示上下文堆是否载入 |
| OPEN_VERSIONS | INTEGER | 显示子游标是否被锁 |
| USERS_OPENING | INTEGER | 任意子游标打开的用户数 |
| USERS_EXECUTING | INTEGER | 任意子游标在执行的用户数 |
| SORTS | BIGINT | 完成的排序数 |
| FETCHES | BIGINT | SQL语句的fetch数 |
| EXECUTIONS | BIGINT | 被载入缓存库后的执行次数 |
| PX_SERVERS_EXECUTIONS | BIGINT | 以并行方式执行的总次数 |
| END_OF_FETCH_COUNT | BIGINT | 光标被带到库缓存中后，该光标完全执行的次数 |
| ROW_PROCESSED | BIGINT | SQL语句返回的总行数 |
| SERIALIZABLE_ABORTS | BIGINT | 事务未能序列化次数 |
| LOADS | BIGINT | SQL进入SQL缓存池的次数 |
| INVALIDATIONS | BIGINT | SQL发生对象数据字典失效的次数 |
| PARSE_CALLS | BIGINT | 解析调用次数 |
| DISK_READS | BIGINT | 读磁盘次数 |
| DIRECT_WRITES | BIGINT | 游标直接写的次数 |
| DIRECT_READS | BIGINT | 游标直接读的次数 |
| BUFFER_GETS | BIGINT | 读缓存区次数 |
| IO_INTERCONNECT_BYTES | BIGINT | 数据库与存储系统之间交换的I/O字节数 |
| PHYSICAL_READ_REQUESTS | BIGINT | SQL发出的物理读取I/O请求数 |
| PHYSICAL_READ_BYTES | BIGINT | SQL读磁盘的字节数 |
| PHYSICAL_WRITE_REQUESTS | BIGINT | SQL发出的物理写入I/O请求数 |
| PHYSICAL_WRITE_BYTES | BIGINT | SQL写入磁盘的字节数 |
| IM_SCANS | BIGINT | 内存列存储段扫描的数量 |
| IM_SCAN_BYTES_UNCOMPRESSED | BIGINT | 内存列存储段扫描的未压缩字节数 |
| IM_SCAN_BYTES_INMEMORY | BIGINT | 内存列存储段扫描的字节数 |
| APPLICATION_WAIT_TIME | BIGINT | 应用等待时间 （单位：微秒）|
| CONCURRENCY_WAIT_TIME | BIGINT | 并发等待时间 （单位：微秒）|
| CLUSTER_WAIT_TIME | BIGINT | 集群等待时间 （保留字段）（单位：微秒）|
| USER_IO_WAIT_TIME | BIGINT | 用户I/O等待时间（单位：微秒） |
| PLSQL_EXEC_TIME | BIGINT | PL执行时间（单位：微秒），保留字段 |
| CPU_TIME | BIGINT | 解析/执行/取得等CPU使用时间（单位：微秒） |
| ELAPSED_TIME | BIGINT | 解析/执行/取得等消耗时间（单位：微秒） |
| COMMAND_TYPE | INTEGER | SQL的命令类型<br>\* 1：SQL_QUERY  <br>\* 2：SQL_INSERT  <br>\* 3：SQL_UPDATE  <br>\* 4：SQL_DELETE  <br>\* 5：SQL_MERGE  <br>\* 6：SQL_WITH  <br>\* 7：SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE | VARCHAR(10) | SQL语句的优化器模型 |
| OPTIMIZER_COST | BIGINT | 优化器给出的本次查询成本 |
| OPTIMIZER_ENV | RAW(2000) | 保留字段 |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT | 保留字段 |
| PARSING_USER_ID | INTEGER | 第一个解析该SQL的用户ID |
| PARSING_SCHEMA_ID | INTEGER | 解析该SQL时所使用的schema ID |
| PARSING_SCHEMA_NAME | VARCHAR(64) | 解析该SQL时所使用的schema名称 |
| ADDRESS | RAW(8) | SQL地址 |
| HASH_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| PLAN_HASH_VALUE | BIGINT | 一个执行计划的唯一标识 |
| MODULE | VARCHAR(64) | 保留字段 |
| MODULE_HASH | BIGINT | 保留字段 |
| ACTION | VARCHAR(64) | 保留字段 |
| ACTION_HASH | BIGINT | 保留字段 |
| OUTLINE_CATEGORY | VARCHAR(64) | 如果应用了一个outline，那么该字段为outline的类别，否则为NULL |
| OUTLINE_SID | INTEGER | 根据该字段可以确定该计划是使用public outline（该字段为NULL）还是private outline（该字段为相应的session sid） |
| CHILD_ADDRESS | RAW(8) | 子游标地址 |
| SQLTYPE | INTEGER | 保留字段 |
| LITERAL_HASH_VALUE | BIGINT | 保留字段 |
| FIRST_LOAD_TIME | DATE | SQL第一次进入SQL缓存池的时间 |
| LAST_LOAD_TIME | DATE | 查询计划加载到库高速缓存的时间 |
| LAST_ACTIVE_TIME | DATE | 上一次访问该SQL的时间 |
| IS_OBSOLETE | VARCHAR(1) | 当子游标的数量太多时，指出游标是否被废弃（Y/N） |
| IS_BIND_SENSITIVE | VARCHAR(1) | 表示SQL语句是否适用于有界计算 |
| BIND_DATA | RAW(2000) | 保留字段 |
| LOCKED_TOTAL | BIGINT | 子光标被锁定的总次数，保留字段|
| PINNED_TOTAL | BIGINT | 子光标固定的总次数 |
| IS_REOPTIMIZABLE | VARCHAR(1) | SQL在执行时实际行数和CBO估算的行数差别很大，需要在下一次执行时进行重新解析 |
| CHILD_NUMBER | INTEGER | 子游标编号 |
| RESTART_STATEMENTS | BIGINT | 语句重启次数 |
