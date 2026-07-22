YashanDB provides statistics functionality, mainly used to collect relevant information about tables, indexes, and columns.

## Collected Content

**Table Statistics**

Includes basic information about all table objects, such as partition tables and temporary tables (global), for example:

- row count: The number of rows in the table.
- block count: The number of data blocks in the table.
- empty block count: A block is considered an empty block when its rows equal 0.
- chaint count: The number of row links/migrations.
- average space: The average free space in a block.
- average row size: Average size of a row.
- sample size: The number of sampled rows.
- last analyzed time: The time when statistics were collected, updated after each collection.

**Index Statistics**

Basic information about the indexes, for example:

- row count: The number of rows in the table.
- leaf block count: The number of leaf blocks.
- first key distinct count: The number of distinct values for the first column of a composite index, used for skip scan.
- all key distinct count: The total number of distinct keys.
- average leaf blocks per key: The number of leaf blocks corresponding to each key; it is 1 for unique indexes.
- average data blocks per key: The number of data blocks corresponding to each key.
- btree level: The level of the BTree.
- cluster factor: The clustering factor of the index, reflecting how well keys correspond to data blocks; the more ordered the rows, the smaller the factor, resulting in lower scan costs.
- last analyzed time: The time when statistics were collected, updated after each collection.

**Column Statistics**

Includes basic information about columns and histograms for columns, for example:

- distinct: The number of unique values in the column.
- null count: The number of null values in the column.
- low/high value: The maximum and minimum values in the column.
- average column length: The average length of the column.
- maximum column length: The maximum length of the column.
- last analyzed time: The time when statistics were collected, updated after each collection.
- density: Density, used for optimizer's estimation of selectivity when no histogram is available.
- number of buckets: The number of histogram buckets.
- sample size: The number of sampled rows.
- frequency histogram: Represented by endpoint number and endpoint value; both values satisfy the following requirements:
  - endpoint number: The cumulative value less than or equal to the current bucket value.
  - endpoint value: The value stored in the current bucket.
- equi-height histogram: Represented by endpoint number and endpoint value; assuming the number of buckets is m (bucket 0 is not counted), both values satisfy the following requirements:
  - endpoint number: Equals the bucket number. Bucket 0 stores the minimum value, and the other buckets are numbered from 1 to m to store the remaining values.
  - endpoint value: The maximum value stored in the current bucket.
- TopN histogram: Similar to the frequency histogram, but only records distinct values for the top number of buckets. The number of distinct values in the top buckets must account for at least (1 - 1/n) * 100% of the rows.
  - endpoint number: The cumulative value less than or equal to the current bucket value.
  - endpoint value: The value stored in the current bucket.
- hybrid histogram: A combination of frequency histogram and equi-height histogram, where endpoint values do not span multiple buckets while also keeping track of the count of endpoint values.
  - endpoint number: The cumulative value less than or equal to the current bucket value.
  - endpoint value: The maximum value within the bucket.
  - repeat cnt: The count of occurrences of the endpoint value.

## Collection Methods

**Dynamic Collection**

The optimizer triggers dynamic collection when it finds that statistics are missing or invalid (missing/stale/insufficient) during the execution plan generation phase.

The OPTIMIZER_DYNAMIC_SAMPLING parameter can be set to configure the level of dynamic collection, with each level defining the conditions for triggering dynamic collection and the sampling rate.

**Manual Collection**

In Standalone Deployment / YAC Deployment, the [DBMS_STATS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) package is used, while in ISC Distributed Cluster Deployment, either the [DBMS_STATS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) package or the [ANALYZE TABLE](../../Development Guide/SQL Reference Manual/SQL Statements/ANALYZE TABLE) statement allows users to manage statistics and manually trigger specific collection actions.

**Automatic Collection**

YashanDB has a built-in scheduled job called GATHER_STATS_JOB, which starts collecting statistics for the entire database daily at `2:00 AM`, including collection for objects with missing or invalid statistics. Users can maintain this scheduled statistics job using the [DBMS_SCHEDULER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_SCHEDULER) package.

Automatic collection of statistics does not apply to ISC Distributed Cluster Deployment.

**Automatic Collection via *yasboot***

The *yasboot* tool implements the functionality for scheduled collection, allowing both Standalone Deployment / ISC Distributed Cluster Deployment to achieve automatic collection functionality via the [job command](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot job).

**Real-Time Collection**

The optimizer triggers a real-time collection action during the execution plan generation phase to obtain real-time statistics. Real-time statistics include some basic information about tables and columns, for example:

-  row count: The number of rows in the table.
- block count: The number of data blocks in the table.
- low/high value: The maximum and minimum values in the column.

The OPTIMIZER_REAL_TIME_STATISTICS parameter can be set to determine whether real-time collection is enabled.

Real-time collection of statistics does not apply to ISC Distributed Cluster Deployment.

## Collection Precision

### Automatic Expiration of Statistics

YashanDB implements an automatic expiration mechanism for statistics. If there are significant changes in the table data (change rate surpasses a specified threshold) or if some DDL changes occur (truncate table/partition, drop partition, add hash partition), the collected statistics for that table are marked as invalid.

**Data Change Monitoring**

When the STATISTICS_LEVEL parameter is configured as typical or all, the system continuously monitors data changes within the table, including DML operations, truncating tables/partitions, etc. These changes can be viewed through the DBA_TAB_MODIFICATIONS, USER_TAB_MODIFICATIONS, and ALL_TAB_MODIFICATIONS views.

**Data Change Persistence**

To ensure performance, change monitoring is done in memory, while DDL changes are immediately persisted. DML changes are periodically (15 minutes) persisted by a background task, and data changes are also persisted before database shutdown. This operation is implemented through the FLUSH_DATABASE_MONITORING_INFO procedure in the [DBMS_STATS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) package, which supports manual user invocation.

**Re-Collection**

The system's built-in job periodically re-collects statistics (for the entire database), or users can manually trigger collection when needed. Users can choose to collect specific objects' information or the entire database's information (online collection for the entire database may consume more system resources and take a longer time).

After re-collecting statistics, the status is restored to valid, allowing the optimizer to obtain accurate statistics and generate reliable execution plans.

**Data Change Reset**

The generated data change information from the monitoring is cleared and reset at the following times:

-  After statistics collection.
- When a table or partition is deleted.

### Statistics Locking and Unlocking

In most cases, users utilize automated tasks for batch statistics collection. A potential issue here is the existence of large tables, such as log tables, which are not valuable for tuning and consume time, thus users may not want them to be continuously collected. Additionally, there may be some tables for which users wish to lock in their perceived optimal statistics state for generating desired execution plans and unlock them later for updating statistics. To address these issues, YashanDB provides statistics locking/unlocking functionality.

Statistics locking/unlocking at the user, table, and partition level can be achieved through the LOCK/UNLOCK relevant procedures in the [DBMS_STATS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) package. Once the specified statistics are locked, all actions related to statistics collection (gather/set/delete, etc.) will no longer affect them.

## Collection Results

Users can view the collected statistics result data through the following views:

|View Name |Description |
| ----------------------- | -------------------------------- |
| DBA_TAB_STATISTICS       | Contains statistics of all collected tables.             |
| DBA_IND_STATISTICS       | Contains statistics of all collected indexes.            |
| DBA_TAB_COL_STATISTICS   | Contains statistics of all collected columns.            |
| DBA_HISTOGRAMS           | Contains histograms of all collected columns.            |
| DBA_PART_COL_STATISTICS  | Contains statistics of all collected partition columns.  |
| DBA_PART_HISTOGRAMS      | Contains histograms of all collected partition columns.   |