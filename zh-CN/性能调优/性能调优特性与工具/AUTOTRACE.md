AUTOTRACE的工作方式类似于EXPLAIN，区别在于AUTOTRACE将执行SQL语句并显示执行结果、每层执行算子的统计信息以及整条SQL语句所产生的统计信息。通过AUTOTRACE用户能够精准地定位到一条SQL执行过程中每层算子消耗的时间、查询/计算出来的行数等信息。如果预估值和实际值有严重偏差，则需要重新收集统计信息。

## 生成AUTOTRACE报告 

### 前提条件

- 如需在报告中生成执行统计信息，需先将STATISTICS_LEVEL参数设置为TYPICAL或ALL。

  > Caution:
  >
  > STATISTICS_LEVEL参数设置为ALL将对系统造成一定性能损失，请确保只在当前SESSION设置该参数。

- 生成报告的过程中会自动查询动态视图，开启并使用AUTOTRACE的用户必须具备查询动态视图的权限（例如内置角色[SELECT_CATALOG_ROLE](../../产品安全/数据访问控制/特权与角色管理/角色)）。

### 操作方法 

1. 以符合权限要求的用户通过yasql登录数据库。

2. 按需配置STATISTICS_LEVEL参数。

    ```sql
    alter session set statistics_level=typical;
    ```

3. 执行[set AUTOTRACE命令](../../工具手册/yasql/yasql使用指导.md#autotrace)即可开启该功能并按需指定报告中包含的信息块。

    ```sql
    SET AUTOTRACE ON
    ```

    开启AUTOTRACE后，在当前会话中执行的DML类语句（SELECT/INSERT/UPDATE/DELETE）时会自动生成相应的执行计划分析报告。

## AUTOTRACE报告简介 

完整的AUTOTRACE报告主要包括以下信息块：

- 执行计划（即Execution Plan区域）

- 执行过程信息（即Operation Information区域）

- 统计信息（即Statistics区域）

### 执行计划 

在AUTOTRACE报告的执行计划区域，相较于标准的[执行计划](执行计划)会额外记录以下信息：

- E - Rows：CBO预估行数。

- A - Rows：实际执行行数。
- A - Time：算子及其所有子算子的实际执行时间总和，单位为微秒。
- Loops：被调用次数。
- Memory：内存资源占用。
- Disk：磁盘资源占用。
- PX RemoteInfo/PX LocalInfo：各节点数据交互的PX执行算子具体信息。
  - total time：激活时间，即开始执行到结束时间，单位为毫秒，取各子线程最大值。
  
  - send_bytes：采样时间内发送的数据量，单位为字节。
  - send_packets：采样时间内发送的包量。
  - send_acks：采样时间内发送的ack数量。
  - wait_space_times：采样时间内因发送缓存不足而等待的次数。
  - wait_space_timeout：采样时间内因发送缓存不足而等待超时的次数。
  - recv_bytes：采样时间内接收的数据量，单位为字节。
  - recv_packets：采样时间内接收的包量。
  - recv_acks：采样时间内接收的ack数量。
  - wait_data_times：采样时间内接收端因数据未到而等待的次数。
  - wait_data_timeout：采样时间内接收端因数据未到而等待超时的次数。

### 执行过程信息 

#### 行式Hash Join 

- Algorithm：Hash Join算法，0表示HDT Hash Join，1表示Partition Hash Join。  

- PartCOunt：分区数。  

- BuildCount：build表总行数，仅在Partition Hash Join中存在。  

- BuildDistinct：build表distinct，仅在Partition Hash Join中存在。  

- BuildSize：build表物化后的大小，仅在Partition Hash Join中存在。  

- IsCacheProbe：是否使用probe表投影缓存，仅在Partition Hash Join中存在。  

#### 向量化Hash Join 

- Estimate Rows：build表预估行数，从统计信息获取。  

- Estimate Row Size：build表每行数据的预估大小，根据表结构推导。  

- Estimate Data Size：build表预估的数据总大小，决定初始分区数。  

- Material Total Size：build表实际物化后的总大小。  

- Real Worker Count：执行build流程的实际线程数。  

- Estimate Partition Count：预估的初始分区数。  

- Force Partition：是否开启强制分区。  

- Real Rows：build表实际有效数据行数。  

- Physical Partition Count：物理分区数，即build表数据实际创建的分区数。  

- Logical Partition Count：逻辑分区数，由于可能存在数据倾斜，执行时会对小的物理分区进行合并，合并后的物理分区为1个逻辑分区。每个逻辑分区数是一个join单元。  

- Partial Logical Partition Count：由于数据倾斜，需要执行Nested Loop Hash Join的分区数。  

- Runtime filters size：runtime filter的大小。  

- Runtime filters create time：构建runtime filter所消耗的时间。  

- Logical Partitions：显示逻辑分区的详细信息，每个逻辑分区对应一个三元组`[物理分区序号, 分区的数据行数, 分区的range数]`，其中若只有一个物理分区其序号为`(n)`，否则为`[start, end)`，例如`(1)`表示分区1，`[1, 4)`表示分区1、2、3。  

#### 行式Partition Hash Group By

- PartCount：分区数。  

- RowSetCount：第一阶段生成的RowSet数量。  

- TotalChunks：总共使用的页面数量。  

- EstimateDistinct：统计信息预估的distinct值。  

- HllEstimateDistinct：HLL算法预估的distinct值。  

- RealDistinct：实际的distinct值。  

- TotalRows：参与hash group的数据行数。  

- MatRows：物化的数据行数。  

- SwapCount：发生SWAP表空间换入换出的次数。  

- Dop：Combine并行度。  

- Is2PhaseAggr：是否采用了两阶段聚集。  

- CombineTime：Combine执行时间。  

#### 行式Merge Join

- Left Child：Sort或Cached，Sort表示需要对左表进行排序并物化，Cached表示直接物化左表数据无需排序。  

- Right Child：Sort或Cached，Sort表示需要对右表进行排序并物化，Cached表示直接物化右表数据无需排序。  

- Key Compare Count：执行join过程中key的比较次数。  

#### 行式Nested Loop Join

- NDV ENABLE：NDV优化的实际状态，1表示开启，0表示关闭。  

- NDV Affected Rows：NDV所影响的行数。  

- NDV Match Rows：左表NDV在哈希表里匹配的行数。  

- NDV Mem Blocks：哈希表物化区所用的页面数，每个页面大小为64K。  

- NDV Mem Key Size：插入到哈希表的key的字节数。  

- NDV Mem Value Size：插入到哈希表的value的字节数。  

- NDV Cost：采用NDV的路径动态计算出来的代价。  

- No NDV Cost：不采用NDV路径动态计算出来的代价。  

- NDV DISABLE REASON：NDV关闭的原因。  

- Right Child：右表是否存在物化。  

- Cached Mem Blocks：右表存在物化时数据所占用的页面数，每个页面大小为64K。  

#### 向量化窗口函数

- WindowHashGroup Count：Radix分区数。  

- Row Count：物化的数据行数。  

- Data Size：物化的数据大小。  

- Partition Time：执行分区物化所用的时间。  

- Order Time：执行排序所用的时间。  

- Batch Window Topn Local Sink Count：开启的topn线程数，0表示未开启。  

- Batch Window Topn Local Sink Finished Count：topn成功完成的线程数，如果等于topn线程数则表示全部执行成功。  

- Batch Window Topn Input Rows：所有topn线程的输入行数。  

- Batch Window Topn Output Rows：所有topn线程的输出行数，通过output/input可以算出topn的过滤率。  

#### 向量化全表扫描

- Runtime filters input rows：执行runtime filter的行数。  

- Runtime filters output rows：runtime filter执行结果为true的行数。  

- Runtime filters use time：执行runtime filter所用的时间。  

### 统计信息 

在AUTOTRACE报告的统计信息区域，会记录以下信息：（下文未列举字段仅用于兼容，无实际含义）

- physical reads：从硬盘读取的数据块数量。

- db block gets：本次SQL请求的读取数据块的数量。
- consistent gets：一致性读的数量。
- redo size：redo产生的字节数。
- recursive calls：递归调用次数。每次执行SQL，为此SQL产生的额外SQL调用次数。
- rows processed：SQL查询/影响的行数。
- bytes sent via PX：网络上PX发送的数据量。
- block received：仅在共享集群中有效，从其他实例接收current block的数量。

### 报告示例 

左连接查询的AUTOTRACE输出如下：

```sql
alter session set statistics_level=all;

set autotrace on;
SELECT b.branch_name, a.area_name
FROM branches b
LEFT OUTER JOIN area a
ON a.area_no = b.area_no
   5 WHERE b.branch_no LIKE '01%' OR b.branch_no LIKE '05%';

BRANCH_NAME                                                      AREA_NAME
---------------------------------------------------------------- -------------------------------------------------------------
Shanghai                                                         EastChina
Nanjing                                                          EastChina
Fuzhou                                                           EastChina
Xiamen                                                           EastChina
Wuhan
Changsha                                                         CentralChin       

Execution Plan
----------------------------------------------------------------
SQL hash value: 3891446547
Optimizer: ADOPT_C                                             
                                                                
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | E - Rows | A - Rows | Cost(%CPU)  | A - Time | Loops    | Memory   | Disk     | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |         6|             |       197|         7|         0|         0|                                |
|  1 |  NESTED LOOPS LEFT OUTER       |                      |            |      2985|         6|        6( 0)|       188|         7|         0|         0|                                |
|  2 |   TABLE ACCESS BY INDEX ROWID  | BRANCHES             | SYS        |      2985|          |        4( 0)|          |          |          |          |                                |
|* 3 |    INDEX RANGE SCAN            | SYS_C_35             | SYS        |      1538|         6|        2( 0)|        45|         7|         0|         0|                                |
|  4 |   TABLE ACCESS BY INDEX ROWID  | AREA                 | SYS        |         1|          |        1( 0)|          |          |          |          |                                |
|* 5 |    INDEX UNIQUE SCAN           | SYS_C_33             | SYS        |         1|         5|        1( 0)|        16|        11|         0|         0|                                |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+


Operation Information (identified by operation id):
---------------------------------------------------


   1 - Execution : NDV ENABLE : 1  NDV Affected Rows : 3  NDV Match Rows : 0  NDV Mem Blocks : 7  NDV Mem Key Size : 18  NDV Mem Value Size : 17  NDV Cost : 0  No NDV Cost : 0
       Nest Loop Join NDV Expression: ("B"."AREA_NO"[OPTMZ-2][OPTMZ-0])
   3 - Predicate : access("B"."BRANCH_NO" LIKE '05%'[OPTMZ-0] OR "B"."BRANCH_NO" LIKE '01%'[OPTMZ-0])
                   filter("B"."BRANCH_NO"[OPTMZ-0] LIKE '05%'[OPTMZ-0] OR "B"."BRANCH_NO"[OPTMZ-0] LIKE '01%'[OPTMZ-0])
   5 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO"[OPTMZ-2][OPTMZ-2])

Statistics
----------------------------------------------------------------------------------------------------
                    0 physical reads
                   18 db block gets
                    0 consistent gets
                    0 redo size
                    0 recursive calls
                    0 bytes sent via SQL*Net to client
                    0 bytes received via SQL*Net from client
                    0 SQL*Net roundtrips to/from client
                    0 sorts (memory)
                    0 sorts (disk)
                    6 rows processed
                    0 bytes sent via PX
                    0 block received


41 rows fetched.
```

physical reads与Disk的值应维持在较低水平。若出现高物理读或硬盘资源占用，极可能源于内存不足，建议调整数据库内存参数或扩容服务器物理内存。

如果预估行数与实际行数偏差较大且收集统计信息后性能未见改善时，可尝试采用[HINT](../SQL调优/SQL调优工具/Hint.md)手动干预执行计划。
