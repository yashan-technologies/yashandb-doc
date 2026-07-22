本视图显示当前所有的SQL执行统计信息（每个SQL一条）。  

|  字段| 类型| 说明|
| --- | --- | --- |
| SQL\_TEXT | VARCHAR(1000) | SQL文本的前1000个字符 |
| SQL\_FULLTEXT | CLOB | SQL CLOB形式的全字符 |
| SQL\_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| SHARABLE\_MEM | INTEGER | 在SQL缓存池中占用的共享内存大小（单位：字节） |
| PERSISTENT\_MEM | INTEGER | 在SQL缓存池中占用的页面内存减去尾部未使用的内存 |
| RUNTIME\_MEM | INTEGER | 此SQL在执行过程中申请的内存（保留字段） |
| LOADED\_VERSIONS | INTEGER | 显示上下文堆是否载入 |
| OPEN\_VERSIONS | INTEGER | 显示子游标是否被锁 |
| USERS\_OPENING | INTEGER | 任意子游标打开的用户数 |
| USERS\_EXECUTING | INTEGER | 任意子游标在执行的用户数 |
| SORTS | BIGINT | 完成的排序数 |
| FETCHES | BIGINT | SQL语句的fetch数 |
| EXECUTIONS | BIGINT | 被载入缓存库后的执行次数 |
| PX\_SERVERS\_EXECUTIONS | BIGINT | 以并行方式执行的总次数（保留字段） |
| END\_OF\_FETCH\_COUNT | BIGINT | 光标被带到库缓存中后，该光标完全执行的次数 |
| ROWS\_PROCESSED | BIGINT | SQL语句返回的总行数 |
| SERIALIZABLE\_ABORTS | BIGINT | 事务未能序列化次数 |
| LOADS | BIGINT | SQL进入SQL缓存池的次数 |
| INVALIDATIONS | BIGINT | SQL发生对象数据字典失效的次数 |
| PARSE\_CALLS | BIGINT | 解析调用次数 |
| DISK\_READS | BIGINT | 读磁盘次数 |
| DIRECT\_WRITES | BIGINT | 游标直接写的次数（保留字段） |
| DIRECT\_READS | BIGINT | 游标直接读的次数（保留字段） |
| BUFFER\_GETS | BIGINT | 读缓存区次数 |
| IO\_INTERCONNECT\_BYTES | BIGINT | 数据库与存储系统之间交换的I/O字节数 |
| PHYSICAL\_READ\_REQUESTS | BIGINT | SQL发出的物理读取I/O请求数 |
| PHYSICAL\_READ\_BYTES | BIGINT | SQL读磁盘的字节数 |
| PHYSICAL\_WRITE\_REQUESTS | BIGINT | SQL发出的物理写入I/O请求数 |
| PHYSICAL\_WRITE\_BYTES | BIGINT | SQL写入磁盘的字节数 |
| IM\_SCANS | BIGINT | 内存列存储段扫描的数量（保留字段） |
| IM\_SCAN\_BYTES\_UNCOMPRESSED | BIGINT | 内存列存储段扫描的未压缩字节数（保留字段） |
| IM\_SCAN\_BYTES\_INMEMORY | BIGINT | 内存列存储段扫描的字节数（保留字段） |
| APPLICATION\_WAIT\_TIME | BIGINT | 应用等待时间（单位：微秒） |
| CONCURRENCY\_WAIT\_TIME | BIGINT | 并发等待时间（单位：微秒） |
| CLUSTER\_WAIT\_TIME | BIGINT | 集群等待时间（保留字段） （单位：微秒）|
| USER\_IO\_WAIT\_TIME | BIGINT | 用户I / O等待时间（单位：微秒） |
| PLSQL\_EXEC\_TIME | BIGINT | PL执行时间（单位：微秒），保留字段 |
| CPU\_TIME | BIGINT | 解析/执行/取得等CPU使用时间（单位：微秒） |
| ELAPSED\_TIME | BIGINT | 解析/执行/取得等消耗时间（单位：微秒） |
| COMMAND\_TYPE | INTEGER | SQL的命令类型<br/>* 1：SQL\_QUERY  <br/>* 2：SQL\_INSERT  <br/>* 3：SQL\_UPDATE  <br/>* 4：SQL\_DELETE  <br/>* 5：SQL\_MERGE  <br/>* 6：SQL\_WITH  <br/>* 7：SQL\_ANONYMOUS\_BLOCK |
| OPTIMIZER\_MODE | VARCHAR(10) | SQL语句的优化器模型（保留字段） |
| OPTIMIZER\_COST | BIGINT | 优化器给出的本次查询成本 |
| OPTIMIZER\_ENV | RAW(2000) | 保留字段 |
| OPTIMIZER\_ENV\_HASH\_VALUE | BIGINT | 保留字段 |
| PARSING\_USER\_ID | INTEGER | 第一个解析该SQL的用户ID |
| PARSING\_SCHEMA\_ID | INTEGER | 解析该SQL时所使用的schema ID |
| PARSING\_SCHEMA\_NAME | VARCHAR(64) | 解析该SQL时所使用的schema名称 |
| ADDRESS | RAW(8) | SQL地址 |
| HASH\_VALUE | BIGINT | SQL的哈希值，由SQL文本计算得到 |
| PLAN\_HASH\_VALUE | BIGINT | 一个执行计划的唯一标识 |
| MODULE | VARCHAR(64) | 在首次解析该SQL语句时正处于执行状态的模块名称，该模块名称通过调用DBMS_APPLICATION_INFO.SET_MODULE进行设置 |
| MODULE\_HASH | BIGINT | 模块名称的哈希值 |
| ACTION | VARCHAR(64) | 在首次解析该SQL语句时正处于执行状态的操作名称，该操作名称通过调用DBMS_APPLICATION_INFO.SET_ACTION进行设置 |
| ACTION\_HASH | BIGINT   |   操作名称的哈希值     |
| OUTLINE\_CATEGORY | VARCHAR(64) | 如果应用了一个outline，那么该字段为outline的类别，否则为NULL |
| OUTLINE\_SID | INTEGER | 根据该字段可以确定该计划是使用public outline（该字段为NULL）还是private outline（该字段为相应的session sid） |
| CHILD\_ADDRESS | RAW(8) | 子游标地址 |
| SQLTYPE | INTEGER | 保留字段 |
| LITERAL\_HASH\_VALUE | BIGINT | 保留字段 |
| FIRST\_LOAD\_TIME | DATE | SQL第一次进入SQL缓存池的时间 |
| LAST\_LOAD\_TIME | DATE | 查询计划加载到库高速缓存的时间 |
| LAST\_ACTIVE\_TIME | DATE | 上一次访问该SQL的时间 |
| IS\_OBSOLETE | VARCHAR(1) | 当子游标的数量太多时，指出游标是否被废弃（Y/N） |
| IS\_BIND\_SENSITIVE | VARCHAR(1) | 表示SQL语句是否适用于有界计算 |
| BIND\_DATA | RAW(2000) | 保留字段 |
| LOCKED\_TOTAL | BIGINT | 子光标被锁定的总次数（保留字段） |
| PINNED\_TOTAL | BIGINT | 子光标固定的总次数 |
| IS\_REOPTIMIZABLE | VARCHAR(1) | SQL在执行时实际行数和CBO估算的行数差别很大，需要在下一次执行时进行重新解析 |
| CHILD\_NUMBER | INTEGER | 子游标编号 |
| RESTART\_STATEMENTS | BIGINT | 语句重启次数 |
| BLOCK\_RECEIVED | BIGINT | 集群下从其他节点获取的最新页面的次数 |
| CR\_BLOCK\_RECEIVED | BIGINT | 集群下从其他节点获取的CR页面的次数 |
| LOCAL\_GRANTS | BIGINT | 集群下本节点授权加载页面的次数 |
| REMOTE\_GRANTS | BIGINT | 集群下其他节点授权加载页面的次数 |
| LOCAL\_UPGRADES | BIGINT | 集群下本节点授权页面锁升级的次数 |
| REMOTE\_UPGRADES | BIGINT | 集群下其他授权页面锁升级的次数 |
| COMPAT\_MODE | INTEGER | 当前SQL的兼容模式 |
| COMPAT\_ATTRS | BIGINT | 当前SQL的兼容模式属性 |
