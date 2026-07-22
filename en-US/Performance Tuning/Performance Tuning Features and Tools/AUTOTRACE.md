AUTOTRACE operates similarly to EXPLAIN, but differs in that AUTOTRACE actually executes the SQL statement and displays both the execution results and statistical information for each level of execution operator, as well as overall statistics for the entire SQL statement. Through AUTOTRACE, users can precisely identify, at each level of the execution plan, the time consumed, number of rows queried/computed, and other metrics. If there is a significant discrepancy between estimated and actual values, statistics should be re-collected.

##  Generate AUTOTRACE Report

### Prerequisites

- To include execution statistics in the report, set the STATISTICS_LEVEL parameter to TYPICAL or ALL beforehand.

  > Caution:
  >
  > Setting the STATISTICS_LEVEL parameter to ALL will incur some performance overhead on the system, so ensure this parameter is set only in the current SESSION.

- During report generation, dynamic views are automatically queried. Users who enable and use AUTOTRACE must have permissions to query dynamic views (e.g., the built-in role [SELECT_CATALOG_ROLE](../../Product Security/Data Access Control/Privilege and Role Management/Roles)).

###  Operation Method

1. Log in to the database via *yasql* using a user with the required permissions.

2. Configure the STATISTICS_LEVEL parameter as needed.

    ```sql
    alter session set statistics_level=typical;
    ```

3. Execute the [set AUTOTRACE](../../Tools Guide/yasql/User Guide for yasql.md#autotrace) command to enable the feature and specify the information blocks to include in the report.

    ```sql
    SET AUTOTRACE ON
    ```

    After enabling AUTOTRACE, execution plan analysis reports will be automatically generated for DML statements (SELECT/INSERT/UPDATE/DELETE) executed in the current session.

##  AUTOTRACE Report Overview

A complete AUTOTRACE report primarily includes the following information sections:

- Execution Plan

- Operation Information

- Statistics

###  Execution Plan

In the Execution Plan section of the AUTOTRACE report, compared to the standard [Execution Plan](./Execution Plans), the following additional information will be recorded:

- E - Rows: Estimated number of rows by CBO.

- A - Rows: Actual number of rows executed.
- A - Time: The total actual execution time of the operator and all its child operators, in microseconds.
- Loops: Number of calls made.
- Memory: Memory resource usage.
- Disk: Disk resource usage.
- PX RemoteInfo/PX LocalInfo: Specific information about PX execution operators for data interaction between nodes.
  - total time: Activation time, i.e., the time from start to finish, in milliseconds, taking the maximum value of all sub-threads.
  
  - send_bytes: Amount of data sent during the sampling period, in bytes.
  - send_packets: Number of packets sent during the sampling period.
  - send_acks: Number of acks sent during the sampling period.
  - wait_space_times: Number of times waited during the sampling period due to insufficient send buffer.
  - wait_space_timeout: Number of times waited during the sampling period due to insufficient send buffer timeout.
  - recv_bytes: Amount of data received during the sampling period, in bytes.
  - recv_packets: Number of packets received during the sampling period.
  - recv_acks: Number of acks received during the sampling period.
  - wait_data_times: Number of times waited during the sampling period due to data not arriving.
  - wait_data_timeout: Number of times waited during the sampling period due to data not arriving timeout.

###  Operation Information

####  Row-based Hash Join  

- Algorithm: Hash Join algorithm, 0 indicates HDT Hash Join, 1 indicates Partition Hash Join.  

- PartCount: Number of partitions.  

- BuildCount: Total number of rows in the build table; only exists in Partition Hash Join.  

- BuildDistinct: Number of distinct values in the build table; only exists in Partition Hash Join.  

- BuildSize: Size of the build table after materialization; only exists in Partition Hash Join.  

- IsCacheProbe: Whether to use projection caching for the probe table; only exists in Partition Hash Join.  

####  Vectorized Hash Join

- Estimate Rows: Estimated number of rows in the build table, obtained from statistics.  

- Estimate Row Size: Estimated size per row in the build table, derived from table structure.  

- Estimate Data Size: Estimated total data size of the build table, used to determine initial partition count.  

- Material Total Size: Total size of the build table after actual materialization.  

- Real Worker Count: Actual number of threads executing the build phase.  

- Estimate Partition Count: Estimated initial partition count.  

- Force Partition: Whether to enable forced partitioning.  

- Real Rows: Actual number of valid data rows in the build table.  

- Physical Partition Count: Number of physical partitions, i.e., the actual number of partitions created for the build table data.  

- Logical Partition Count: Number of logical partitions. Due to potential data skew, small physical partitions may be merged during execution, with each merged physical partition forming one logical partition. Each logical partition represents a join unit.  

- Partial Logical Partition Count: Number of partitions requiring Nested Loop Hash Join due to data skew.  

- Runtime filters size: Size of runtime filter.  

- Runtime filters create time: Time consumed to build runtime filter.  

- Logical Partitions: Displays detailed information about logical partitions. Each logical partition corresponds to a triplet `[physical partition number, number of rows in the partition, number of ranges in the partition]`. If there is only one physical partition, the number is `(n)`, otherwise it is `[start, end)`, for example `(1)` indicates partition 1, `[1, 4)` indicates partitions 1, 2, and 3.  

#### Row-based Partition Hash Group By

- PartCount: Number of partitions.  

- RowSetCount: Number of RowSets generated in the first phase.  

- TotalChunks: Total number of pages used.  

- EstimateDistinct: Estimated distinct value from statistics.  

- HllEstimateDistinct: Estimated distinct value from the HLL algorithm.  

- RealDistinct: Actual distinct value.  

- TotalRows: Number of data rows participating in hash group.  

- MatRows: Number of materialized data rows.  

- SwapCount: Number of times SWAP tablespace swap-in and swap-out occurred.  

- Dop: Combine parallel degree.  

- Is2PhaseAggr: Whether two-phase aggregation was used.  

- CombineTime: Execution time for Combine.  

#### Row-based Merge Join

- Left Child: Sort or Cached. Sort indicates that the left table must be sorted and materialized; Cached indicates that the left table data is materialized directly without sorting.  

- Right Child: Sort or Cached. Sort indicates that the right table must be sorted and materialized; Cached indicates that the right table data is materialized directly without sorting.  

- Key Compare Count: Number of key comparisons during the join execution.  

#### Row-based Nested Loop Join

- NDV ENABLE: Actual state of NDV optimization, 1 indicates enabled, 0 indicates disabled.  

- NDV Affected Rows: Number of rows affected by NDV.  

- NDV Match Rows: Number of rows matching left table NDV in the hash table.  

- NDV Mem Blocks: Number of pages used in the hash table materialization area, each page is 64K in size.  

- NDV Mem Key Size: Number of bytes used for keys inserted into the hash table.  

- NDV Mem Value Size: Number of bytes used for values inserted into the hash table.  

- NDV Cost: Cost dynamically calculated for the path using NDV.  

- No NDV Cost: Cost dynamically calculated for the path without using NDV.  

- NDV DISABLE REASON: Reason for disabling NDV.  

- Right Child: Whether the right table is materialized.  

- Cached Mem Blocks: Number of pages occupied by data when the right table is materialized, each page is 64K in size.  

#### Vectorized Window Function(s)

- WindowHashGroup Count: Number of Radix partitions.  

- Row Count: Number of materialized data rows.  

- Data Size: Size of materialized data.  

- Partition Time: Time consumed to execute partition materialization.  

- Order Time: Time consumed to execute sorting.  

- Batch Window Topn Local Sink Count: Number of topn threads enabled; 0 indicates not enabled.  

- Batch Window Topn Local Sink Finished Count: Number of topn threads successfully completed; if equal to the number of topn threads, it indicates all executions were successful.  

- Batch Window Topn Input Rows: Total number of input rows across all topn threads.  

- Batch Window Topn Output Rows: Total number of output rows across all topn threads; the topn filtering rate can be calculated from output/input.  

#### Vectorized Full Table Scan

- Runtime filters input rows: Number of rows processed by runtime filter.  

- Runtime filters output rows: Number of rows for which runtime filter evaluation returned true.  

- Runtime filters use time: Time consumed to execute runtime filter.

###  Statistics 

In the Statistics section of the AUTOTRACE report, the following information will be recorded: (Fields not listed below are for compatibility only and have no actual meaning.)

- physical reads: Number of data blocks read from disk.

- db block gets: Number of data blocks read for this SQL request.
- consistent gets: Number of consistent reads.
- redo size: Number of bytes generated for redo.
- recursive calls: Number of recursive calls. This is the number of additional SQL calls generated for each execution of the SQL.
- rows processed: Number of rows queried/affected by the SQL.
- bytes sent via PX: Amount of data sent over the network.
- block received: Valid only in YAC, indicating the number of current blocks received from other instances.

###  Example

The AUTOTRACE output for a left join query is as follows:

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

The values of physical reads and Disk should remain at a low level. If high physical reads or disk resource consumption occur, it is likely due to insufficient memory; consider adjusting database memory parameters or increasing server physical memory.

If the estimated row count significantly deviates from the actual row count and performance does not improve after collecting statistics, you may attempt to manually influence the execution plan using [HINT](../SQL Tuning/SQL Tuning Tools/Hint).
