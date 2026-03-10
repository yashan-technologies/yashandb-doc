AUTOTRACE的工作方式类似于EXPLAIN，区别在于AUTOTRACE将执行SQL语句，并显示执行结果，每层执行算子的统计信息，以及整条SQL语句所产生的统计信息（后两项可通过AUTOTRACE选项控制是否显示）。通过启动AUTOTRACE，用户能够精准定位到一条SQL执行过程中每层算子消耗的时间，查询/计算出来的行数等信息。如果预估值和实际值有严重偏差，则需要重新收集统计信息。

> **Note**: 
>
> 统计信息显示是否有内容依赖于系统是否已开启统计信息收集开关（STATISTICS_LEVEL参数设置为ALL）。
>
> 当STATISTICS_LEVEL参数设置为TYPICAL或ALL时会统计表的变化情况，同时也会收集每个计划算子的执行时间、执行次数等信息。
>
> STATISTICS_LEVEL参数设置为ALL将对系统造成一定性能损失，请确保只在当前SESSION设置该参数。
> 
> 生成AUTOTRACE的过程中会查询动态视图，开启并使用AUTOTRACE的用户必须具备查询动态视图的权限（例如[内置角色](../../产品安全/数据访问控制/特权与角色管理/角色)SELECT_CATALOG_ROLE）。

典型的AUTOTRACE输出如下：

```sql
ALTER SESSION SET statistics_level=ALL;


SET autotrace ON;
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME         DHQ                   
------- ----------------- --------------------- 
01      EastChina            Shanghai             
02      WestChina            Chengdu              
03      SouthChina            Guangzhou            
04      NorthChina            Beijing              
05      CentralChina            Wuhan                

Execution Plan                                                   
---------------------------------------------------------------- 
SQL hash value: 4040703571                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | E - Rows | A - Rows | Cost(%CPU)  | A - Time | Loops    | Memory   | Disk     | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |         5|             |        29|         5|          |          |                                |
|  1 |  TABLE ACCESS FULL             | AREA                 | SYS        |    100000|         5|      442( 0)|        20|         5|          |          |                                |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+

Statistics
-------------------------------------------------------------------
                    0 physical reads                               
                    6 db block gets                                
                    0 consistent gets                              
                    0 redo size                                    
                    0 recursive calls                              
                    0 bytes sent via SQL**** to client             
                    0 bytes received via SQL**** from client       
                    0 SQL**** roundtrips to/from client            
                    0 sorts (memory)                               
                    0 sorts (disk)                                 
                    5 rows processed
                  359 bytes sent via PX                                                                                                      

```

开启AUTOTRACE时的执行计划输出将比关闭时多一些信息，如下：

- **E - Rows**：CBO预估行数。
- **A - Rows**：实际执行行数。
- **A - Time**：算子实际执行时间。
- **Loops**：被调用次数。
- **Memory**：内存资源占用。
- **Disk**：磁盘资源占用。
- **recursive calls**：递归调用次数。每次执行SQL，为此SQL产生的额外SQL调用次数。
- **db block gets**：本次SQL请求的读取数据块的数量。
- **consistent gets**：一致性读的数量。
- **physical reads**：从硬盘读取的数据块数量。
- **redo size**：redo产生的字节数。
- **rows processed**：SQL查询/影响的行数。
- **bytes sent via PX**：网络上PX发送的数据量。

其中，physical reads和Disk的数值应处在较低水平，如果产生了大量的物理读和硬盘资源消耗，很大概率是内存不足引起，需要调整数据库的内存参数，或调整服务器物理内存。

如果预估的行数和实际行数偏差较大，而且收集统计信息后性能仍未改善，可使用HINT手动调整执行计划。
