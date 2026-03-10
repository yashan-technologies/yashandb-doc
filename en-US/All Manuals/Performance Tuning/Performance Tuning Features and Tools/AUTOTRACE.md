The way AUTOTRACE works is similar to EXPLAIN, with the difference that AUTOTRACE executes the SQL statement and displays the execution results, statistics of each execution operator at each level, and the overall statistics for the SQL statement (the latter two can be controlled for display via AUTOTRACE options). By enabling AUTOTRACE, users can pinpoint the time consumed by each operator in the SQL execution process, the number of rows retrieved/calculated, and other information. If there is a significant deviation between the estimated values and the actual values, statistics need to be collected again.

> **Note**: 
>
> The display of statistics depends on whether the system has enabled the statistics collection switch (STATISTICS_LEVEL parameter set to ALL).
>
> When the STATISTICS_LEVEL parameter is set to TYPICAL or ALL, changes to the table will be recorded, and information about each plan operator's execution time and execution count will also be collected.
>
> Setting the STATISTICS_LEVEL parameter to ALL will incur some performance overhead on the system, so ensure this parameter is set only in the current SESSION.
> 
> During the generation of AUTOTRACE, dynamic views are queried. Users enabling and using AUTOTRACE must have the privilege to query dynamic views (for example, [built-in role](../../Product Security/Data Access Control/Privilege and Role Management (yashan Mode)/Roles) SELECT_CATALOG_ROLE).

A typical AUTOTRACE output is as follows:

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

When AUTOTRACE is enabled, the execution plan output will include more information than when it is disabled, as follows:

- **E - Rows**: Estimated number of rows by CBO.
- **A - Rows**: Actual number of rows executed.
- **A - Time**: Actual execution time of the operator.
- **Loops**: Number of calls made.
- **Memory**: Memory resource usage.
- **Disk**: Disk resource usage.
- **recursive calls**: Number of recursive calls. This is the number of additional SQL calls generated for each execution of the SQL.
- **db block gets**: Number of data blocks read for this SQL request.
- **consistent gets**: Number of consistent reads.
- **physical reads**: Number of data blocks read from disk.
- **redo size**: Number of bytes generated for redo.
- **rows processed**: Number of rows queried/affected by the SQL.
- **bytes sent via PX**: Amount of data sent over the network.

Among these, the values for physical reads and Disk should remain at a low level. If a large number of physical reads and disk resource consumption occur, it is likely caused by insufficient memory, requiring adjustments to database memory parameters or the server's physical memory.

If there is a large deviation between the estimated row count and the actual row count, and performance has not improved after collecting statistics, HINTs can be used to manually adjust the execution plan.