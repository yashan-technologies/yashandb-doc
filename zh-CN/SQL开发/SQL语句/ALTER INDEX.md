## 通用描述

ALTER INDEX用于修改索引的相关信息。

一个SQL命令行可以指定多项修改操作，以`,`分开；但对于存在分区索引的表，该表上的索引操作需单项执行，即不能放在同一个命令行中，且对于分区索引，其修改操作必须按指定分区进行，而不能针对整体索引操作。

## 语句定义

**alter index::=**

```ebnf+diagram
syntax::= ALTER INDEX [schema"."] index_name (INITRANS integer|VISIBLE|INVISIBLE|UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY]|NOPARALLEL|PARALLEL integer|NOLOGGING|LOGGING |modify_partition|modify_subpartition|rebuild_clause|RENAME TO new_name|reclaim_index_clause|COMPUTE STATISTICS)
```

**[modify\_partition](#modifypartition)::=**

```ebnf+diagram
syntax::= MODIFY PARTITION partition_name (INITRANS integer|UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY])
```

**[modify\_subpartition](#modifysubpartition)::=**

```ebnf+diagram
syntax::= MODIFY SUBPARTITION subpartition_name (UNUSABLE|COALESCE [CLEANUP | CLEANUP ONLY])
```

**[rebuild\_clause](#rebuildclause)::=**

```ebnf+diagram
syntax::= REBUILD [(PARTITION partition_name)|(SUBPARTITION subpartition_name)|(NOREVERSE|REVERSE)] [(TABLESPACE tablespace_name|INITRANS integer|PCTFREE integer|ONLINE|(NOCOMPRESS|COMPRESS (integer))|(LOGGING|NOLOGGING)|(NOPARALLEL|PARALLEL integer)|COMPUTE STATISTICS) {" " (TABLESPACE tablespace_name|INITRANS integer|PCTFREE integer|ONLINE|(NOCOMPRESS|COMPRESS (integer))|(LOGGING|NOLOGGING)|(NOPARALLEL|PARALLEL integer)|COMPUTE STATISTICS)}]
```

**[reclaim\_index_clause](#reclaimindexclause)::=**

```ebnf+diagram
syntax::= RECLAIM SEGMENT [PARTITION partition_name] TABLESPACE tablespace_name  [OBJNO_REUSE object_id SEG_FILE file_id SEG_BLOCK block_id]
```

### 1. INITRANS

该语句用于修改索引的数据块（Data Block）里事务（Transaction）表的初始大小设置，修改成功后该索引后续增加的数据块将按照此值设置事务表初始大小。

示例（HEAP表、TAC表）

```sql
ALTER INDEX idx_sales_info_1 INITRANS 4;
```

### 2. VISIBLE|INVISIBLE

该语句用于设置索引对优化器（Optimizer）是否可见，即在执行SQL查询时优化器是否会考虑使用该索引。

示例（HEAP表、TAC表）

```sql
ALTER INDEX idx_sales_info_1 INVISIBLE;
 
ALTER INDEX idx_sales_info_1 VISIBLE;
```

### 3. UNUSABLE

该语句用于设置索引为不可用状态，之后对表操作不会触发该索引更新，因此如需要恢复该索引可用，则必须进行索引[重建（REBUILD）](#rebuildclause)。

>**Caution**:
>
> 临时表上的索引被置为不可用状态后无法恢复，请谨慎操作。如需恢复，只能先删除该不可用索引再重新创建同名索引。

示例（HEAP表、TAC表）

```sql
ALTER INDEX idx_sales_info_1 UNUSABLE;
```

<span id="coalesce" name="coalesce" class="yaslink"></span>

### 4. COALESCE

该语句用于重组索引，RTree索引无法重组。

对于分区索引，只可对实际分配了空间的分区进行重组。

当BTree索引（尤其是单调递增索引）使用一段时间后，整个BTree上可能会出现大量的空页和稀疏页，造成空间浪费，为解决这一问题，YashanDB提供索引coalesce功能，包括对空页的回收及稀疏页的合并，但此功能并不会使BTree的树高度变低。

重组操作会对索引产生结构性变更，且会对索引基表加表级共享锁，因此建议在没有业务或者业务量很低时执行本操作。

当指定CLEANUP时，会在COALESCE的过程中清理索引中的孤儿键值对。如果索引上的键值对指向的segment已不复存在，则称为孤儿键值对。

当指定CLEANUP ONLY时，不会进行COALESCE，只会清理孤儿键值对。

建库时默认会创建一个每天凌晨2点清理全库包含孤儿键值对索引的定时任务PMO_DEFERRED_GIDX_MAINT_JOB，它会调用[DBMS_PART](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_PART)中的CLEANUP_GIDX_JOB。

示例

```sql
ALTER INDEX idx_finance_info_1 COALESCE;
ALTER INDEX idx_finance_info_1 COALESCE CLEANUP;
ALTER INDEX idx_finance_info_1 COALESCE CLEANUP ONLY;
```

### 5. NOPARALLEL|PARALLEL

该语句用于语法兼容，无实际含义。

### 6. NOCOMPRESS|COMPRESS

该语句用于语法兼容，无实际含义。

### 7. LOGGING|NOLOGGING

该语句用于语法兼容，无实际含义。

<span id="modifypartition" name="modifypartition" class="yaslink"></span>

### 8. COMPUTE STATISTICS

该语句用于语法兼容，无实际含义。

### 9. modify\_partition

该语句用于对索引的分区进行INITRANS/UNUSABLE/COALESCE等修改操作，对于分区索引，必须通过本语句进行指定的分区索引修改。

示例

```sql
--通过系统视图查看分区索引信息
SELECT index_name,partition_name,status
FROM USER_IND_PARTITIONS
WHERE index_name='IDX_SALES_INFO_1';
INDEX_NAME             PARTITION_NAME         STATUS
---------------------- ---------------------- ---------
IDX_SALES_INFO_1       IP_SALES_INFO_1        N/A
IDX_SALES_INFO_1       IP_SALES_INFO_2        N/A
IDX_SALES_INFO_1       IP_SALES_INFO_3        N/A   

--对指定分区索引进行修改
ALTER INDEX idx_sales_info_1 MODIFY PARTITION ip_sales_info_1 UNUSABLE;
```

<span id="modifysubpartition" name="modifysubpartition" class="yaslink"></span>

### 10. modify\_subpartition

该语句用于对索引的二级分区进行INITRANS/UNUSABLE/COALESCE等修改操作，对于组合分区索引，必须通过本语句进行指定的二级分区索引修改。

示例

```sql
--对指定二级分区索引进行修改
ALTER INDEX idx_sales_info_1 MODIFY SUBPARTITION isp_sales_info_11 UNUSABLE;
ALTER INDEX idx_sales_info_1 MODIFY SUBPARTITION isp_sales_info_21 COALESCE;
```

<span id="rebuildclause" name="rebuildclause" class="yaslink"></span>

### 11. rebuild\_clause

该语句用于对索引或者索引分区进行重建操作。

当索引使用了一段时间后，可能存在空间膨胀或占用空间过多的问题，此时可以通过rebuild功能进行重建，生成新的紧凑的BTree。此外，通过指定TABLESPACE参数，重建索引也可以实现索引表空间搬迁。

对于被设置UNUSABLE的索引，重建后该索引将恢复为有效状态。

对于分区索引的REBUILD操作，需在每个分区上单独执行。一级分区索引只能REBUILD PARTITION，二级分区索引只能REBUILD SUBPARTITION。

本操作过程中将会在索引基表上加排他锁，但在指定了ONLINE时，本操作不会阻塞并发的DML操作。

重建索引的使用限制同[创建索引](./CREATE INDEX)。

#### 11.1. PARTITION

该语句用于重建索引的指定分区。

**partition_name**

指定需重建的索引分区的名称，必须为已存在的分区。

#### 11.2. SUBPARTITION

该语句用于重建索引的指定二级分区。

**subpartition_name**

指定需重建的索引二级分区的名称，必须为已存在的二级分区。

示例（HEAP表，单机TAC表，单机LSC表）

```sql
ALTER INDEX idx_sales_info_1 REBUILD SUBPARTITION isp_sales_info_11;
```

#### 11.3. NOREVERSE|REVERSE

重建索引为反向或非反向，使用限制同[CREATE INDEX](./CREATE INDEX.html#REVERSE)。

示例（HEAP表，单机TAC表）

```sql
ALTER INDEX idx_finance_info_1 REBUILD REVERSE;

ALTER INDEX idx_finance_info_1 REBUILD NOREVERSE;
```

#### 11.4. TABLESPACE

重建索引或索引分区到指定的表空间。

重建分布表上的索引或索引分区时，不能指定表空间。

#### 11.5. INITRANS

为重建的索引或索引分区指定INITRANS值。

#### 11.6. PCTFREE

为重建的索引或索引分区指定PCTFREE值。

#### 11.7. ONLINE

该语句用于指定重建索引过程中是否允许并发DML操作，省略则默认不允许，使用限制同[CREATE INDEX](./CREATE INDEX.html#ONLINE)。

示例（单机HEAP表、单机TAC表）

```sql
ALTER INDEX idx_finance_info_1 REBUILD TABLESPACE yashan INITRANS 3 PCTFREE 10 ONLINE;
```

#### 11.8. NOCOMPRESS|COMPRESS

该语句用于语法兼容，无实际含义。

#### 11.9. NOLOGGING|LOGGING

该语句用于语法兼容，无实际含义。

#### 11.10. COMPUTE STATISTICS

该语句用于语法兼容，无实际含义。

#### 11.11. NOPARALLEL|PARALLEL

设置重建索引的并行度，NOPARALLEL表示不并行，不指定并行度默认按一半CPU核数的并行度并发创建索引，使用限制同[CREATE INDEX](./CREATE INDEX.html#parallel)。

**integer**

并行度值，取值范围为[1,服务器CPU核数\*2]，可省略。省略时，当探测到当前表数据量为大于1G，小于当前DATA_BUFFER_SIZE参数值时，则默认按一半CPU核数的并行度并发创建索引。

示例（HEAP表、单机TAC表）

```sql
ALTER INDEX idx_finance_info_1 REBUILD PARALLEL 2;
```

### 12. RENAME TO new\_name

该语句用于指定修改索引的名称。  

指定的新名称不能为空且必须符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

示例

```sql
ALTER INDEX idx_sales_info_1 RENAME TO idx_sales_info_2;
```

<span id="reclaimindexclause" name="reclaimindexclause" class="yaslink"></span>

### 13. reclaim\_index\_clause

该语句用于在表空间迁移后，为迁移的对象声明存储对象。

> **Warn**:
>
> 通常用于在存算一体分布式集群部署中进行节点/节点组扩缩容后，将逻辑对象与物理存储对象进行关联。
>
> 在该场景下，数据库会自动生成reclaim语句并自动在目标节点执行。**不建议用户手动执行该语句**，若不避免可能会导致系统崩溃或其他不可预知的结果。
