通用描述
----

SET AUTOTRACE为在yasql（YashanDB的SQL客户端工具）中实现的命令，用于生成trace报告，得到DML类型SQL语句（SELECT/INSERT/UPDATE/DELETE）的执行计划，以及执行计划实际产生的资源消耗和执行时间等信息。

当开启AUTOTRACE后，在客户端执行的DML类SQL语句，除输出SQL结果外，还将输出trace报告中的信息，信息内容依据AUTOTRACE的开启选项决定。

AUTOTRACE是常见的SQL调优工具，可在调优手册中获取更多信息。

语句定义
----

**set autotrace::=**

```ebnf+diagram
syntax::= SET AUTOT[RACE] {ON | OFF | TRACE[ONLY]} [EXP[LAIN]] [STAT[ISTICS]]
```

### 1. SET AUTOTRACE OFF

默认模式，不生成trace报告。

示例

```sql
SET AUTOTRACE OFF;
```

### 2. SET AUTOTRACE ON EXPLAIN

生成trace报告，且输出语句的执行结果和执行计划。

开启此设置后，后续执行的DML语句将输出执行计划，且执行计划中新增如下信息：

- A-ROWS：实际执行行数
- A-TIME：实际执行时间，单位为微秒
- LOOP：数据提取次数
- MEMORY：算子占用的内存大小
- DISK：算子占用的磁盘空间大小
- PX RemoteInfo/PX LocalInfo：各节点数据交互的PX执行算子具体信息
  - total time：激活时间，即开始执行到结束时间，单位毫秒，取各子线程最大值
  - send_bytes：采样时间内发送的数据量，单位为字节
  - send_packets：采样时间内发送包的个数
  - send_acks：采样时间内发送ack的个数
  - wait_space_times：采样时间内因发送缓存不足而等待的次数
  - wait_space_timeout：采样时间内因发送缓存不足而等待超时的次数
  - recv_bytes：采样时间内接收的数据量，单位为字节
  - recv_packets：采样时间内接收包的个数
  - recv_acks：采样时间内接收ack的个数
  - wait_data_times：采样时间内接收端因数据未到来而等待的次数
  - wait_data_timeout：采样时间内接收端因数据未到来而等待超时的次数

上述新增相比的是在开启设置之前由[EXPLAIN](./EXPLAIN)语句输出的内容，且在开启设置之后执行[EXPLAIN](./EXPLAIN)语句将同样新增上述信息。

示例

```sql
SET AUTOTRACE ON EXPLAIN;
```

### 3. SET AUTOTRACE ON STATISTICS

生成trace报告，且输出语句的执行结果和SQL执行统计信息（统计信息仅在STATISTICS_LEVEL参数为'ALL'模式下记录）。

开启此设置后，后续执行的DML语句将输出SQL执行所产生的redo日志大小、物理读、一致性读和递归调用等资源消耗数据。

示例

```sql
SET AUTOTRACE ON STATISTICS;
```

### 4. SET AUTOTRACE ON

生成trace报告，且输出语句的执行结果、执行计划和执行统计信息。

示例

```sql
SET AUTOTRACE ON;
```

### 5. SET AUTOTRACE TRACEONLY

生成trace报告，且不输出语句的执行结果，只输出语句的执行计划和执行统计信息。

示例

```sql
SET AUTOTRACE TRACEONLY;
```
