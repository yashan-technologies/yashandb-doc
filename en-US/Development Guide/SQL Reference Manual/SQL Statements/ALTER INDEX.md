## General Description

ALTER INDEX is used to modify the relevant information of an index.

A SQL command line can specify multiple modification operations, separated by `,`; however, for tables with partitioned indexes, index operations on that table must be executed one at a time, meaning they cannot be combined in the same command line, and for partitioned indexes, modification operations must be performed on the specified partition rather than the entire index.

## Statement Definition

**alter index::=**

```ebnf
= ALTER INDEX [schema"."] index_name (INITRANS integer|(VISIBLE|INVISIBLE)|UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY]|(NOPARALLEL|PARALLEL integer)|(NOLOGGING|LOGGING)|modify_partition|modify_subpartition|rebuild_clause|RENAME TO new_name|reclaim_index_clause|COMPUTE STATISTICS).
```

**[modify\_partition](#modifypartition)::=**

```ebnf
= MODIFY PARTITION partition_name (INITRANS integer|UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY]).
```

**[modify\_subpartition](#modifysubpartition)::=**

```ebnf
= MODIFY SUBPARTITION subpartition_name (UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY]).
```

**[rebuild\_clause](#rebuildclause)::=**

```ebnf
= REBUILD [(PARTITION partition_name)|(SUBPARTITION subpartition_name)|(NOREVERSE|REVERSE)] [(TABLESPACE tablespace_name|INITRANS integer|PCTFREE integer|ONLINE|(NOCOMPRESS|COMPRESS (integer))|(LOGGING|NOLOGGING)|(NOPARALLEL|PARALLEL integer)|COMPUTE STATISTICS) {" " (TABLESPACE tablespace_name|INITRANS integer|PCTFREE integer|ONLINE|(NOCOMPRESS|COMPRESS (integer))|(LOGGING|NOLOGGING)|(NOPARALLEL|PARALLEL integer)|COMPUTE STATISTICS)}].
```

**[reclaim\_index_clause](#reclaimindexclause)::=**

```ebnf
= RECLAIM SEGMENT [PARTITION partition_name] TABLESPACE tablespace_name  [OBJNO_REUSE object_id SEG_FILE file_id SEG_BLOCK block_id].
```

### INITRANS

This statement is used to modify the initial size setting of the transaction table in the index's data block (Data Block). After successful modification, subsequent data blocks added to this index will use this value to set the initial size of the transaction table.

***Example*** for Heap tables and TAC tables

```sql
ALTER INDEX idx_sales_info_1 INITRANS 4;
```

### VISIBLE|INVISIBLE

This statement is used to set whether the index is visible to the optimizer. That is, whether the optimizer will consider using the index when executing SQL queries.

***Example*** for Heap tables and TAC tables

```sql
ALTER INDEX idx_sales_info_1 INVISIBLE;
 
ALTER INDEX idx_sales_info_1 VISIBLE;
```

### UNUSABLE

This statement is used to set the index to an unusable state, which means that subsequent operations on the table will not trigger updates to the index. If it is necessary to restore the usability of the index, the index must be [rebuilt (REBUILD)](#rebuildclause).

>**Caution**:
>
> After an index on a temporary table is set to an unusable state, it cannot be restored — please operate with caution. To restore, you must drop the unusable index and then recreate the index with the same name.

***Example*** for Heap tables and TAC tables

```sql
ALTER INDEX idx_sales_info_1 UNUSABLE;
```

<span id="coalesce" name="coalesce"></span>

### COALESCE

This statement is used to reorganize the index; RTree indexes cannot be reorganized.

For partitioned indexes, only actual allocated partitions can be reorganized.

When BTree indexes (especially monotonically increasing indexes) have been used for a period of time, there may be a significant number of empty pages and sparse pages across the entire BTree, leading to wasted space. To address this issue, YashanDB provides the index coalesce functionality, which includes reclaiming empty pages and merging sparse pages; however, this functionality does not reduce the height of the BTree.

Reorganization will cause structural changes to the index and will acquire a table-level shared lock on the index base table, so it is recommended to execute this operation when there is no business activity or when the business volume is very low.

When CLEANUP is specified, orphan key-value pairs in the index will be cleaned up during the COALESCE process. If the segments pointed to by the key-value pairs in the index no longer exist, they are defined as orphan key-value pairs.

When CLEANUP ONLY is specified, COALESCE will not proceed; only orphan key-value pairs will be cleaned up.

By default, a scheduled task PMO_DEFERRED_GIDX_MAINT_JOB is created when the database is initialized, which cleans up orphan key-value pairs in all indexes at 2 AM every day. It will call the CLEANUP_GIDX_JOB in [DBMS_PART](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_PART).

***Example***

```sql
ALTER INDEX idx_finance_info_1 COALESCE;
ALTER INDEX idx_finance_info_1 COALESCE CLEANUP;
ALTER INDEX idx_finance_info_1 COALESCE CLEANUP ONLY;
```

### NOPARALLEL|PARALLEL

This statement is used for syntax compatibility and has no actual meaning.

### NOCOMPRESS|COMPRESS

This statement is used for syntax compatibility and has no actual meaning.

### LOGGING|NOLOGGING

This statement is used for syntax compatibility and has no actual meaning.

<span id="modifypartition" name="modifypartition"></span>

### COMPUTE STATISTICS

This statement is used for syntax compatibility and has no actual meaning.

### modify_partition

This statement is used to modify the partition of an index with operations such as INITRANS/UNUSABLE/COALESCE, etc. For partitioned indexes, specified partition index modifications must be performed through this statement.

***Example***

```sql
-- View partition index information through system view
SELECT index_name,partition_name,status
FROM user_ind_partitions
WHERE index_name='IDX_SALES_INFO_1';
INDEX_NAME             PARTITION_NAME         STATUS
---------------------- ---------------------- ---------
IDX_SALES_INFO_1       IP_SALES_INFO_1        N/A
IDX_SALES_INFO_1       IP_SALES_INFO_2        N/A
IDX_SALES_INFO_1       IP_SALES_INFO_3        N/A   

-- Modify the specified partition index
ALTER INDEX idx_sales_info_1 MODIFY PARTITION ip_sales_info_1 UNUSABLE;
```

<span id="modifysubpartition" name="modifysubpartition"></span>

### modify_subpartition

This statement is used to modify the second-level partition of an index with operations such as UNUSABLE/COALESCE, etc. For composite partition indexes, specified second-level partition index modifications must be performed through this statement.

***Example***

```sql
-- Modify the specified second-level partition index
ALTER INDEX idx_sales_info_1 MODIFY SUBPARTITION isp_sales_info_11 UNUSABLE;
ALTER INDEX idx_sales_info_1 MODIFY SUBPARTITION isp_sales_info_21 COALESCE;
```

<span id="rebuildclause" name="rebuildclause"></span>

### rebuild\_clause

This statement is used to perform a rebuild operation on the index or index partition.

When an index has been used for a period of time, there may be issues with space expansion or excessive space consumption. In such cases, the rebuild functionality can be used to generate a new compact BTree. Additionally, by specifying the TABLESPACE parameter, rebuilding the index can also achieve index tablespace migration.

For indexes set to UNUSABLE, rebuilding will restore them to a valid state.

For REBUILD operations on partitioned indexes, each partition must be executed separately. One-level partitioned indexes can only REBUILD PARTITION, and two-level partitioned indexes can only REBUILD SUBPARTITION.

An exclusive lock will be acquired on the index base table during this operation, but if ONLINE is specified, this operation will not block concurrent DML operations.

Rebuild index usage restrictions are the same as those for [creating indexes](CREATE INDEX).

#### PARTITION

This statement is used to rebuild a specified partition of the index.

**partition\_name**

Specifies the name of the index partition to be rebuilt, which must be an existing partition.

#### SUBPARTITION

This statement is used to rebuild a specified second-level partition of the index.

**subpartition\_name**

Specifies the name of the index second-level partition to be rebuilt, which must be an existing second-level partition.

***Example*** for Heap tables, Standalone Deployment TAC tables, Standalone Deployment LSC tables

```sql
ALTER INDEX idx_sales_info_1 REBUILD SUBPARTITION isp_sales_info_11;
```

#### NOREVERSE|REVERSE

Rebuilding the index can be done in reverse or non-reverse order, with usage restrictions the same as [CREATE INDEX](CREATE INDEX.md#REVERSE).

***Example*** for Heap tables, Standalone Deployment TAC tables

```sql
ALTER INDEX idx_finance_info_1 REBUILD REVERSE;

ALTER INDEX idx_finance_info_1 REBUILD NOREVERSE;
```

#### TABLESPACE

Rebuild the index or index partition to the specified tablespace.

When rebuilding indexes or index partitions on sharded tables, tablespace cannot be specified.

#### INITRANS

Specify INITRANS value for the rebuilt index or index partition.

#### PCTFREE

Specify PCTFREE value for the rebuilt index or index partition.

#### ONLINE

This statement is used to specify whether concurrent DML operations are allowed during the index rebuilding process. If omitted, it defaults to not allowing, with usage restrictions the same as [CREATE INDEX](CREATE INDEX.md#ONLINE).

***Example*** for Standalone Deployment Heap tables and Standalone Deployment TAC tables

```sql
ALTER INDEX idx_finance_info_1 REBUILD TABLESPACE yashan INITRANS 3 PCTFREE 10 ONLINE;
```

#### NOCOMPRESS|COMPRESS

This statement is used for syntax compatibility and has no actual meaning.

#### NOLOGGING|LOGGING

This statement is used for syntax compatibility and has no actual meaning.

#### COMPUTE STATISTICS

This statement is used for syntax compatibility and has no actual meaning.

#### NOPARALLEL|PARALLEL

Set the degree of parallelism for rebuilding the index. NOPARALLEL indicates no parallelism; if no degree of parallelism is specified, it defaults to a degree of parallelism equal to half the number of CPU cores. Usage restrictions are the same as [CREATE INDEX](CREATE INDEX.md#parallel).

**integer**

The degree of parallelism, within the range [1, server CPU cores * 2], can be omitted. When omitted, if it is detected that the current table data volume is greater than 1G and less than the current DATA_BUFFER_SIZE parameter value, it will default to a degree of parallelism equal to half the number of CPU cores.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
ALTER INDEX idx_finance_info_1 REBUILD PARALLEL 2;
```

### RENAME TO new_name

This statement is used to specify the new name of the index.

The new name specified cannot be empty and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

***Example***

```sql
ALTER INDEX idx_sales_info_1 RENAME TO idx_sales_info_2;
```

<span id="reclaimindexclause" name="reclaimindexclause"></span>

### reclaim\_index_clause

This statement is used to declare storage objects for migrated objects after tablespace migration.

> **Warn**:
>
> Generally used in ISC Distributed Cluster Deployment during node/node group scaling after associating logical objects with physical storage objects.
>
> In this scenario, the database will automatically generate reclaim statements and execute them on the target nodes automatically. **Manual execution of this statement by users is not recommended**, as it may lead to system crashes or other unpredictable outcomes.