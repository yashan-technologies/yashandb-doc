#### 导入结束后为什么会显示`[YASLDR] execute finished with warnings`而非`[YASLDR] execute succeeded`？

yasldr工具默认开启容错特性，以便跳过错误数据继续执行导入。若导入结束后显示`[YASLDR] execute finished with warnings`，说明导入过程中遇到了错误数据，请检查提示的日志文件进行确认。

#### 导入过程中为什么会报`no free cursors in cursor pool`的错误？

该问题是由当前并发数太高导致cursor不足引起的，可通过调整系统参数SHARE_POOL_SIZE增加可用的缓存池大小，或稍后重试。

#### 导入过程中为什么会报`no free blocks in large pool`的错误？

该问题是由目标端数据库的参数LARGE_POOL_SIZE设置较小引起的，建议调整LARGE_POOL_SIZE。
    
若此参数调整为最大值后仍出现该问题，建议：
1. 调整导入表，减少导入heap分区表的分区总个数。
2. 降低导入时的degree_of_parallelism参数。
3. 重新导入。
    
#### 导入过程中为什么会报`the table is corrupted`的错误？
    
当参数NOLOGGING设置为TRUE时，如果导入过程中有数据违反了目标表的约束条件，导入工具无法容错，会报错显示the table is corrupted。
    
请将NOLOGGING设置为FALSE再导入，或保证数据满足目标表的约束条件后再重新导入。

#### 导入过程中为什么会报`dead lock detected`的错误？

该问题是由于待导入的表上存在索引或约束引起的。yasldr采用并行导入，并行的线程在插入数据时，若存在索引或约束，则可能触发该问题。

请将senders参数设置为1，或者去除索引或约束后再导入。

#### 导入过程中为什么会报`no line break found in csv block buffer`的错误？

yasldr在处理数据时，需要先将数据从磁盘加载到内存buffer中，然后再进行逐行解析，若单次读入buffer中的数据没有包含完整的行，则会报此错误。

请调整csv_chunk_size或csv_line_size的参数取值，以便内存buffer可以包含完整的行后再次执行导入。

#### 为什么导入时长激增？

导入过程中需要查询系统表和系统视图，获取表的元信息后将数据解析并进行类型转换后发送到服务端。导入时长激增的可能原因如下：

- 频繁变更元数据，例如数据迁移评估场景下频繁创建和删除表，导致系统当前的统计信息不准确而无法计算出最优执行计划，查询元信息过慢而导入变慢。此时可通过手动执行统计信息收集后再次重试导入。

- 导入过程中统计信息收集正在运行，导致数据库进程争抢资源而数据处理变慢，进而导致导入变慢。此时可暂时关闭统计信息收集，待导入结束后，再次开启统计信息收集功能。

#### 为什么时序数据导入时间分区的分区表特别慢？

yasldr在导入分区数据时，按照分区对解析的csv数据进行组织管理，每一个分区预先分配一定数量的slot来存储数据。在时序数据导入时，reader线程获取的数据很大概率上属于同一个分区，导致分区slot竞争激烈。

同时slot用完后，需要等待sender线程发送完毕后才可使用，进而导致reader无slot可用而进入等待状态，造成导入速度下降。

此种业务模式下导入时，可以通过设置SLICE_POLICY=RANDOM SENDER_TIMEOUT=0命令行参数来缓解分区冲突造成的性能下降。
