## 通用描述

ALTER TABLE用于修改数据库里的表的结构和定义，以及对表进行相关管理操作，包括：

- [表更名](#RENAMETO)
- [修改列](#altercolumnclause)
- [修改分区](#altertablepartition)、[合并分区](#mergetablesubpartitions)
- [修改约束](#altertableconstraint)
- 开启和关闭行迁移（[row movment](#rowmovementclause)）
- 开启和关闭附加日志（[supplemental logging](#supplementaltablelogging)）
- 开启和关闭redo日志（[nologging](#loggingclause)）
- 开启和关闭并行 ([parallel](#parallelclause))
- 空间收缩（[shrink space](#shrinkspaceclause)）
- LSC表的后台数据转换选项开关（[data transformer](#enablexfmrclause)）
- LSC表的可变数据生命周期（[MCOL time to live](#mcolttlclause)）
- LSC表开启或关闭MCOL功能 （[MCOL ability](#mcolabilityclause)）
- LSC表的强制转换（[force xfmr](#forcexfmrclause)）

其中，依据LSC表的存储特性，只能对其分区相关属性（但不包括分区索引）进行修改操作（包括增加分区和删除分区等）。

不能对[AC对象](../基本SQL元素/对象)以及AC对象的源表执行ALTER TABLE操作。

语句定义
----

**alter table::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name 
(alter_table_properties
|alter_column_clause
|alter_table_partition
|alter_table_constraint
|reclaim_table_clause)
```

**[alter\_table\_properties](#altertableproperties)::=**

~~~ebnf+diagram
syntax::= (RENAME TO new_table_name)
|row_movement_clause
|supplemental_table_logging
|shrink_space_clause
|lsc_properties_clause
|logging_clause
|parallel_clause
|readonly_clause
~~~

**[row_movement_clause](#rowmovementclause)::=**

~~~ebnf+diagram
syntax::= ((ENABLE)|(DISABLE)) ROW MOVEMENT
~~~

**[supplemental\_table\_logging](#supplementaltablelogging)::=**

```ebnf+diagram
syntax::= add_supplemental_logging_clause
|drop_supplemental_logging_clause
```

**[add\_supplemental\_logging\_clause](#addsupplementalloggingclause)::=**

```ebnf+diagram
syntax::= ADD SUPPLEMENTAL LOG DATA "("(PRIMARY KEY|UNIQUE|ALL)")" COLUMNS
```

**[drop\_supplemental\_logging\_clause](#dropsupplementalloggingclause)::=**

```ebnf+diagram
syntax::= DROP SUPPLEMENTAL LOG DATA
```

**[shrink_space_clause](#shrinkspaceclause)::=**

~~~ebnf+diagram
syntax::= SHRINK SPACE [COMPACT] [CASCADE]
~~~

**[lsc_properties_clause](#lscpropertiesclause)::=**

```ebnf+diagram
syntax::= enable_xfmr_clause|mcol_ttl_clause|mcol_ability_clause|force_xfmr_clause
```

**[enable_xfmr_clause](#enablexfmrclause)::=**

~~~ebnf+diagram
syntax::= (ENABLE|DISABLE) ((TRANSFORM | COMPACT | BUILD AC){" "(TRANSFORM | COMPACT| BUILD AC)})
~~~

**[mcol_ttl_clause](#mcolttlclause)::=**

```ebnf+diagram
syntax::= ALTER MCOL TTL timestamp
```

**[mcol_ability_clause](#mcolabilityclause)::=**

```ebnf+diagram
syntax::= (ENABLE|DISABLE) MCOL [order by]
```

**[force_xfmr_clause](#forcexfmrclause)::=**

```ebnf+diagram
syntax::= ALTER SLICE ALL (STABLE | COMPACT | CLEAN) [ASYNC]
```

**[logging_clause](#loggingclause)::=**

```ebnf+diagram
syntax::= LOGGING [ASYNC]|NOLOGGING
```

**[parallel\_clause](#parallelclause)::=**

```ebnf+diagram
syntax::= NOPARALLEL | PARALLEL [integer]
```

**[readonly_clause](#readonlyclause)::=**

```ebnf+diagram
syntax::= READONLY | READWRITE
```

**[alter_column_clause](#altercolumnclause)::=**

```ebnf+diagram
syntax::= add_column_clause
|drop_column_clause
|rename_column_clause
|modify_column_clause
```

**[add\_column\_clause](#addcolumnclause)::=**

```ebnf+diagram
syntax::= ADD [COLUMN] "(" (column_definition) {"," (column_definition)} ")"
[lob_clauses]
```

**[column\_definition](#columndefinition)::=**

```ebnf+diagram
syntax::= column dataType [(DEFAULT default_expr | inline_constraint | column_encryption_clause)
{" " (DEFAULT default_expr | inline_constraint | column_encryption_clause)}]
```

**[inline_constraint定义](../通用SQL语法/constraint（yashan模式）)**

**[lob\_clauses](#lobclause)::=**

```ebnf+diagram
syntax::= (lob_clause) {" " (lob_clause)}
```
**[lob\_clause](#lobclause)::=**

```ebnf+diagram
syntax::= LOB "(" (column) {"," (column)} ")" STORE AS [BASICFILE|SECUREFILE]
"("(TABLESPACE space_name|(ENABLE|DISABLE) STORAGE IN ROW)")"
```

**[drop\_column\_clause](#dropcolumnclause)::=**

```ebnf+diagram
syntax::= DROP drop_column
```

**[drop\_column](#dropcolumn)::=**

```ebnf+diagram
syntax::= [COLUMN] "(" (column_name) {"," (column_name)} ")"
```

**[rename\_column\_clause](#renamecolumnclause)::=**

```ebnf+diagram
syntax::= RENAME [COLUMN] old_name TO new_name
```

**[modify_column_clause](#modifycolumnclause)::=**

```ebnf+diagram
syntax::= MODIFY (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)}) 
{"," (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)})}
```

**[alter\_table\_partition](#altertablepartition)::=**

```ebnf+diagram
syntax::= add_table_partition
|drop_table_partition
|drop_table_subpartition
|truncate_table_partiton
|truncate_table_subpartition
|set_partition_clause
|modify_partition_clause
|split_table_partition
|merge_table_partition
|merge_table_subpartition
```

**[add\_table\_partition](#addtablepartition)::=**

```ebnf+diagram
syntax::= ADD (((PARTITION [partname] add_range_partition_clause)
{"," (PARTITION [partname] add_range_partition_clause)})| 
	((PARTITION [partname] add_list_partition_clause)
    {"," (PARTITION [partname] add_list_partition_clause)})| 
	(PARTITION [partname] add_hash_partition_clause))
```

**[add\_range\_partition\_clause](#addrangepartitionclause)::=**

```ebnf+diagram
syntax::= range_values_clause [table_partition_description]
[("(" (
 ((range_subpartition_desc) {"," (range_subpartition_desc)})
 | ((list_subpartition_desc) {"," (list_subpartition_desc)})
 | (individual_hash_subparts { "," individual_hash_subparts})) ")")]
```

**[range\_values\_clause](#addrangepartitionclause)::=**

```ebnf+diagram
syntax::= VALUES LESS THAN "(" (literal|MAXVALUE) {"," (literal|MAXVALUE)} ")"
```

**[table\_partition\_description](#tablepartitiondescription)::=**

```ebnf+diagram
syntax::= (TABLESPACE tablespace|PCTFREE integer|PCTUSED integer|INITRANS integer|MAXTRANS integer)
```

**[add\_list\_partition\_clause](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= list_values_clause [table_partition_description]
[("(" (
 ((range_subpartition_desc) {"," (range_subpartition_desc)})
 | ((list_subpartition_desc) {"," (list_subpartition_desc)})
 | (individual_hash_subparts { "," individual_hash_subparts})) ")")]
```

**[list\_values\_clause](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= VALUES "(" (DEFAULT|list_values) ")"
```

**[list\_values](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= ((literal|NULL) {"," (literal|NULL)})
|(("(" ((literal|NULL) {"," (literal|NULL)})")") {"," ("(" ((literal|NULL) {"," (literal|NULL)})")")})
```

**[add\_hash\_partition\_clause](#addhashpartitionclause)::=**

```ebnf+diagram
syntax::= [partition_storage_clause]
[("(" (
 ((range_subpartition_desc) {"," (range_subpartition_desc)})
 | ((list_subpartition_desc) {"," (list_subpartition_desc)})
 | (individual_hash_subparts { "," individual_hash_subparts})) ")")]
```

**[partition\_storage\_clause](#partitionstorageclause)::=**

```ebnf+diagram
syntax::= TABLESPACE tablespace
```

**[drop\_table\_partition](#droptablepartition)::=**

```ebnf+diagram
syntax::= DROP PARTITION ((partname) {"," (partname)}) [update_index_clause]
```

**[update\_index\_clause](#updateindexclause)::=**

```ebnf+diagram
syntax::= [UPDATE | INVALIDATE] GLOBAL INDEXES
```

**[drop\_table\_subpartition](#droptablepartition)::=**

```ebnf+diagram
syntax::= DROP SUBPARTITION ((subpartname) {"," (subpartname)}) [update_index_clause]
```

**[truncate\_table\_partition](#truncatetablepartition)::=**

```ebnf+diagram
syntax::= TRUNCATE PARTITION ((partname) {"," (partname)}) [truncate_part_clause]
```

**[truncate\_part\_clause](#truncatepartclause)::=**

```ebnf+diagram
syntax::= ((DROP|REUSE) STORAGE)|CASCADE|PURGE
```

**[truncate\_table\_subpartition](#truncatetablepartition)::=**

```ebnf+diagram
syntax::= TRUNCATE SUBPARTITION ((subpartname) {"," (subpartname)}) [truncate_part_clause]
```

**[set\_partition\_clause](#setpartitionclause)::=**

```ebnf+diagram
syntax::= SET ((INTERVAL "(" [expr] ")" )
| (STORE IN "(" ((tablespace) {"," (tablespace)}) ")"))
```

**[modify_partition_clause](#modifypartitionclause)::=**

~~~ebnf+diagram
syntax::= MODIFY (PARTITION | SUBPARTITION) partname (shrink_space_clause | add_subpartition_clause)
~~~

**[split\_table\_partition](#splittablepartition)::=**

```ebnf+diagram
syntax::= SPLIT PARTITION partname
    (AT "(" literal ")" [INTO "(" range_partition_desc "," range_partition_desc ")"] |
     VALUES "(" list_values ")" [INTO "(" list_partition_desc "," list_partition_desc ")"] |
     INTO "(" ((range_partition_desc) {"," (range_partition_desc)} | (list_partition_desc) {"," (list_partition_desc)}) "," PARTITION [partname] [table_partition_description] ")")
    [UPDATE [GLOBAL] INDEXES]
```

**[add_subpartition_clause](#addsubpartitionclause)::=**

```ebnf+diagram
syntax::= ADD
 (((range_subpartition_desc) {"," (range_subpartition_desc)})
 | ((list_subpartition_desc) {"," (list_subpartition_desc)})
 | (individual_hash_subparts))
```

**[merge\_table\_partitions](#mergetablepartitions)::=**

```ebnf+diagram
syntax::= MERGE PARTITIONS partition_or_key_value ((partition_or_key_value) {"," (partition_or_key_value)} | "TO" partition_or_key_value) [INTO PARTITION [partname] [table_partition_description]] [UPDATE [GLOBAL] INDEXES]
```

**[partition_or_key_value](#partitionorkeyvalue)::=**

```ebnf+diagram
syntax::= partition name
|FOR "(" partition_key_value{","(partition_key_value)} ")"
```

**[merge\_table\_subpartitions](#mergetablepartitions)::=**

```ebnf+diagram
syntax::= MERGE SUBPARTITIONS subpartition_or_key_value ((subpartition_or_key_value) {"," (subpartition_or_key_value)} | "TO" subpartition_or_key_value) [INTO SUBPARTITION [partname] [table_partition_description]] [UPDATE [GLOBAL] INDEXES]
```

**[subpartition_or_key_value](#subpartitionorkeyvalue)::=**

```ebnf+diagram
syntax::= subpartition name
|FOR "(" partition_key_value{","(partition_key_value)} ")"
```

**[alter\_table\_constraint](#altertableconstraint)::=**

```ebnf+diagram
syntax::= add_constraint
|drop_constraint
|modify_constraint
|enable_disable_constraint
```

**[add\_constraint](#addconstraint)::=**

```ebnf+diagram
syntax::= ADD "(" (out_of_line_constraint) {"," (out_of_line_constraint)} ")" 
```

**[out_of_line_constraint定义](../通用SQL语法/constraint（yashan模式）)**

**[drop\_constraint](#dropconstraint)::=**

```ebnf+diagram
syntax::= DROP 
((PRIMARY KEY) 
	| (UNIQUE "("  ((column_name) {"," (column_name)}) ")")
	| (CONSTRAINT constraint_name)) 
[CASCADE] 
[(KEEP | DROP) INDEX]
```

**[modify\_constraint](#modifyconstraint)::=**

```ebnf+diagram
syntax::= MODIFY 
((PRIMARY KEY)
	| (UNIQUE "(" ((column_name) {"," (column_name)}) ")")
	| (CONSTRAINT constraint_name))
(ENABLE|DISABLE) [VALIDATE|NOVALIDATE]
[CASCADE]
```

**[enable\_disable\_constraint](#enabledisableconstraint)::=**

```ebnf+diagram
syntax::= (ENABLE|DISABLE) [VALIDATE|NOVALIDATE]
((PRIMARY KEY)
	| (UNIQUE "("  ((column_name) {"," (column_name)}) ")")
    | (CONSTRAINT constraint_name))
[using_index_clause]
[CASCADE]
[(KEEP|DROP) INDEX]
```

**[reclaim_table_clause](#reclaimtableclause)::=**

```ebnf+diagram
syntax::= RECLAIM SEGMENT [PRIMARY_SPACE tablespace_name PARTITION partition_name] TABLESPACE tablespace_name  [OBJNO_REUSE object_id SEG_FILE file_id SEG_BLOCK block_id] [COLSEGS "(" DATAOBJ data_objectid TYPE type ID id ENTRY entry FLAG flag ")"] 
[LOBS "(" lob_col [OBJNO_REUSE object_id SEG_FILE file_id SEG_BLOCK block_id ] INDEX  [OBJNO_REUSE object_id SEG_FILE file_id SEG_BLOCK block_id ] ")"] [AUXSEGS "(" AUXENTRY entry ")"]
```

<span id="altertableproperties" name="altertableproperties" class="yaslink"></span>

### 1. alter\_table\_properties

该语句用于修改表的一系列属性。

<span id="RENAMETO" name="RENAMETO" class="yaslink"></span>

#### 1.1. RENAME TO

该语句用于修改表名，指定的新名称不能为空且必须符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

示例

```sql
ALTER TABLE area RENAME TO area_new;
ALTER TABLE area_new RENAME TO area;
```

<span id="rowmovementclause" name="rowmovementclause" class="yaslink"></span>

#### 1.2. row\_movement\_clause

该语句用于开启或关闭表的行迁移功能，参考[CREATE TABLE](./CREATE TABLE)中相应功能描述。

示例（单机HEAP表、单机TAC表）

```sql
-- 创建一张DISABLE ROW MOVEMENT的分区表
DROP TABLE IF EXISTS orders_info_rowmove;
CREATE TABLE orders_info_rowmove (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY RANGE (id)
(PARTITION p1 VALUES LESS THAN (800),
PARTITION p2 VALUES LESS THAN (1800),
PARTITION p3 VALUES LESS THAN (2800));
  
-- 插入数据
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',1300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',2300);
COMMIT;
  
-- 查询p1分区的记录
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove PARTITION(p1);
ORDER_NO          ORDER_DESC   AREA  BRANCH ORDER_DATE           SALESPERSON      ID
----------------- ------------ ----- ------ -------------------- ------------- -----
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            300
 
-- 更新分区列值，未开启行迁移时本语句报错
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
YAS-02209 ROW MOVEMENT is not enabled

-- 执行开启
ALTER TABLE orders_info_rowmove ENABLE ROW MOVEMENT;
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
 
-- 重新查询p1分区的记录，之前在p2分区的数据被移动到了此分区中
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove PARTITION(p1);
ORDER_NO          ORDER_DESC   AREA  BRANCH ORDER_DATE           SALESPERSON      ID
----------------- ------------ ----- ------ -------------------- ------------- -----
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            300
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            500

-- 跨分区更新数据结束后，建议关闭行迁移
ALTER TABLE orders_info_rowmove DISABLE ROW MOVEMENT;
```

示例（分布式TAC表）

```sql
-- 创建一张DISABLE ROW MOVEMENT的TAC表
DROP TABLE IF EXISTS orders_info_rowmove;
CREATE TABLE orders_info_rowmove (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY HASH (area)
SUBPARTITION BY RANGE (id)
SUBPARTITION TEMPLATE(
SUBPARTITION sp1 VALUES LESS THAN (800),
SUBPARTITION sp2 VALUES LESS THAN (1800),
SUBPARTITION sp3 VALUES LESS THAN (2800))
(PARTITION p1,
PARTITION p2,
PARTITION p3,
PARTITION p4,
PARTITION p5,
PARTITION p6,
PARTITION p7,
PARTITION p8,
PARTITION p9,
PARTITION p10,
PARTITION p11,
PARTITION p12,
PARTITION p13,
PARTITION p14,
PARTITION p15,
PARTITION p16,
PARTITION p17,
PARTITION p18,
PARTITION p19,
PARTITION p20,
PARTITION p21);
  
-- 插入数据
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',1300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',2300);
COMMIT;
  
-- 查询p1中sp1子分区的记录(由于分布式一级分区为HASH分区，实际结果可能与本例中结果不同)
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove SUBPARTITION(p1_sp1);
ORDER_NO          ORDER_DESC                                                       AREA  BRANCH ORDER_DATE                       SALESPERSON            ID
----------------- ---------------------------------------------------------------- ----- ------ -------------------------------- ------------- -----------
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  300
 
-- 更新分区列值，未开启行迁移时本语句报错
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
YAS-02209 ROW MOVEMENT is not enabled

-- 执行开启
ALTER TABLE orders_info_rowmove ENABLE ROW MOVEMENT;
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
 
-- 重新查询p1中sp1子分区的记录，之前在sp2子分区的数据被移动到了此分区中
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove SUBPARTITION(p1_sp1);
ORDER_NO          ORDER_DESC                                                       AREA  BRANCH ORDER_DATE                       SALESPERSON            ID
----------------- ---------------------------------------------------------------- ----- ------ -------------------------------- ------------- -----------
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  300
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  500

-- 跨分区更新数据结束后，建议关闭行迁移
ALTER TABLE orders_info_rowmove DISABLE ROW MOVEMENT;
```

<span id="supplementaltablelogging" name="supplementaltablelogging" class="yaslink"></span>

#### 1.3. supplemental\_table\_logging

该语句用于开启或关闭表级附加日志属性。表级附加日志只对当前的表生效，数据库级附加日志请参考[ALTER DATABASE](./ALTER DATABASE.html#supplementallogclauses)。

分布式部署中用户无法执行此操作。

如需使用[Ystream服务](../../PL参考手册/内置高级包/DBMS_YSTREAM_ADM)，应先开启库级附加日志（库级或表级）再创建Ystream服务，若先创建了Ystream服务再开启附加日志会报错。

<span id="addsupplementalloggingclause" name="addsupplementalloggingclause" class="yaslink"></span>

##### 1.3.1. add\_supplemental\_logging\_clause

该语句用于为该表开启附加日志属性，开启附加日志后，对该表执行DML或DDL类型语句，将会在redo日志里记录额外的信息，以便逻辑日志解析工具能从redo日志里解析出DML和DDL语句。尤其是UPDATE和DELETE语句，会额外记录目标行的主键，唯一键或整行（除LOB列）数据到redo日志里，因此对数据库性能有影响。

附加日志有三种类型：

- PRIMARY KEY：在UPDATE，DELETE的redo日志中，仅记录该行的主键列的值。
- UNIQUE：在UPDATE，DELETE的redo日志中，仅记录该行的非空的唯一索引列的值。
- ALL：在UPDATE，DELETE的redo日志中，记录该行的所有列（LOB型，32K以上varchar和char等除外）的值。

附加日志约束项：

- 不能对已开启附加日志的表重复执行ADD SUPPLEMENTAL LOG操作。
- 不能对附加日志类型为PRIMARY KEY或UNIQUE的表，删除附加日志依赖的索引列。
- 不能对没有主键的表开启PRIMARY KEY类型的附加日志。
- 不能对没有非空唯一索引列的表开启UNIQUE类型的附加日志。
- 不能对唯一索引列开启了[加密](#columnencryptionclause)的表开启UNIQUE类型的附加日志。
- 不能对使用了字典编码的TAC表开启附加日志。
- 不能对开启附加日志的表，执行长度超过32K的DDL语句。

通过[DBA_LOG_GROUPS](../../../参考手册/系统视图/DBA视图/DBA_LOG_GROUPS)、[ALL_LOG_GROUPS](../../../参考手册/系统视图/ALL视图)、[USER_LOG_GROUPS](../../../参考手册/系统视图/USER视图)视图可以查看当前已开启的附加日志信息。

示例（单机部署）
```sql
ALTER TABLE area ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
ALTER TABLE branches ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
```

<span id="dropsupplementalloggingclause" name="dropsupplementalloggingclause" class="yaslink"></span>

##### 1.3.2. drop\_supplemental\_logging\_clause

该语句用于关闭表上的附加日志属性。

示例（单机部署）

```sql
ALTER TABLE area DROP SUPPLEMENTAL LOG DATA;
```

<span id="shrinkspaceclause" name="shrinkspaceclause" class="yaslink"></span>

#### 1.4. shrink\_space\_clause

该语句用于对表或分区进行收缩，使得数据存储更加紧凑，降低segment的高水位线，释放空闲出来的连续的extent。

该语句仅适用于HEAP表。

由于收缩数据涉及到物理位置变更，执行本操作前必须开启行迁移，具体操作请查阅[row_movement_clause](#rowmovementclause)。

**COMPACT**

指定此关键字表示只将数据存储紧凑，降低水位线，并不释放空出来的extent。

**CASCADE**

指定此关键字表示在收缩表的同时，对其上索引也进行收缩，仅用于语法兼容，无实际意义。

示例（HEAP表）

```sql
ALTER TABLE orders_info ENABLE ROW MOVEMENT;
ALTER TABLE orders_info SHRINK SPACE;
ALTER TABLE orders_info SHRINK SPACE COMPACT;
ALTER TABLE orders_info SHRINK SPACE COMPACT CASCADE;
```

<span id="lscpropertiesclause" name="lscpropertiesclause" class="yaslink"></span>

#### 1.5. lsc\_properties\_clause

该语句只用于修改LSC表的相关属性，对其他类型表执行这些修改将会报错。

<span id="enablexfmrclause" name="enablexfmrclause" class="yaslink"></span>

##### 1.5.1. enable\_xfmr\_clause

该语句用于控制LSC表的后台数据转换能力。

目前的YashanDB后台提供给LSC表如下数据转换能力：

- TRANSFORM：LSC的可变数据依据此开关转换为稳态数据存储，可变数据缓冲区的数据被转换，转移到稳态数据内。关闭此开关即不再允许自动转换。需要注意，如果一直不转换，数据将一直存放在可变数据区，无法对数据存储格式进行优化，不建议长时间关闭。
- COMPACT：LSC的稳态数据依据此开关将数据格式进行自动优化（包括排序和合并）。关闭此开关即不再允许自动优化。需要注意，稳态数据的优化有利于查询性能提升，但是优化期间会产生额外的资源消耗，可以根据业务要求适当关闭。
- BUILD AC：LSC的稳态数据依据此开关进行AC的数据文件生成。关闭此开关即不再允许自动生成AC的数据文件，AC的能力可根据业务自行关闭开启。

此子句允许同时开启或关闭多种能力，用户也可通过配置DATA_TRANSFORMER_ENABLED参数整体开启或关闭后台数据转换能力。

> **Note**：
>
> 1.TRANSFORM和COMPACT涉及物理空间变动， 转换任务执行后将在一段时间内保留转换前的数据，用于满足长查询的需要；当达到保留的最大时间时对这些数据进行清理 。用户在确保业务不受影响的情况下，可参照[force_xfmr_clause](#forcexfmrclause)执行立即清理。
>
> 2.系统中不存在AC对象时，打开BUILD AC开关不会创建AC数据文件。

示例（LSC表）

```sql
ALTER SYSTEM SET DATA_TRANSFORMER_ENABLED = TRUE SCOPE=SPFILE;
ALTER TABLE orders_info ENABLE TRANSFORM;
ALTER TABLE orders_info ENABLE TRANSFORM BUILD AC COMPACT;
ALTER TABLE orders_info DISABLE TRANSFORM;
ALTER TABLE orders_info DISABLE COMPACT;
```

<span id="mcolttlclause" name="mcolttlclause" class="yaslink"></span>

##### 1.5.2. mcol\_ttl\_clause

该语句用于修改LSC表的可变生命周期，其含义请参考[CREATE TABLE](./CREATE TABLE)中对应语句描述。

该语句不适用于分布式部署。

示例（单机LSC表）

```sql
--将finance_info的可变生命周期由1个月修改为10天
ALTER TABLE finance_info ALTER MCOL TTL '10' DAY;

--area表创建时未设置可变生命周期，将其设为10年
ALTER TABLE area ALTER MCOL TTL '10' YEAR(9);
```

<span id="mcolabilityclause" name="mcolabilityclause" class="yaslink"></span>

##### 1.5.3. mcol\_ability\_clause

该语句用于开启或关闭LSC表的可变数据（MCOL）整体功能或排序功能，排序含义请参考[CREATE TABLE](./CREATE TABLE)中table_sort_clause语句描述。

关闭MCOL功能后，对LSC表进行的数据插入或修改操作会在提交时立即变成不可变数据，如果业务没有较高的事务要求，建议关闭此模块功能。

如需关闭某个LSC表的MCOL功能，请确保已开启该表的后台数据转换能力（ENABLE TRANSFORM）。

示例（LSC表）

```sql
ALTER TABLE orders_info ENABLE MCOL ORDER BY;
ALTER TABLE orders_info DISABLE MCOL ORDER BY;
ALTER TABLE orders_info ENABLE MCOL;

-- 如需对某个表DISABLE MCOL，需先对其ENABLE TRANSFORM
ALTER TABLE orders_info ENABLE TRANSFORM;
ALTER TABLE orders_info DISABLE MCOL;
```

<span id="forcexfmrclause" name="forcexfmrclause" class="yaslink"></span>

##### 1.5.4. force\_xfmr\_clause

该语句用于LSC表强制转换，其中ASYNC标记表示异步转换，省略情况下默认进行同步转换。

强制转换有如下三种模式：

- STABLE：将LSC表的可变数据强制转换成稳态数据并生成此表下所有AC数据。
- COMPACT：将LSC表的稳态数据强制进行合并。
- CLEAN：将LSC的所有可删除数据（ 转换任务执行完成后达到延期清理条件的数据 ）强制进行删除。

示例（LSC表）

```sql
ALTER TABLE sales_info ALTER SLICE ALL STABLE;
ALTER TABLE sales_info ALTER SLICE ALL COMPACT;
ALTER TABLE sales_info ALTER SLICE ALL CLEAN;
```

<span id="loggingclause" name="loggingclause" class="yaslink"></span>

#### 1.6. logging\_clause

该语句用于修改表的logging属性，logging属性用于指定表记录日志的方式。

该语句不适用于分布式部署。

##### 1.6.1. LOGGING

该语句用于将表转为logging属性，即对该表的所有操作记录日志。

- 若对已是logging属性的表执行该语句，直接返回成功。
- 若对nologging属性的表执行该语句，系统将执行一次全量checkpoint，将数据写盘并修改flushback，最后修改表的logging属性。

##### 1.6.2. LOGGING ASYNC

该语句用于异步将表转为logging属性。启动新线程完成表模式转化的操作从而不阻塞主线程工作。

注意当客户端返回成功时并不保证转换一定成功，只代表启动线程成功。后续转换仍有可能失败。转换结果通过[运行日志](../../../数据库管理/日志管理/运行日志管理)记载。

在异步转换表的过程中存在如下约束限制：

- 不能对转换中的表做任意DML操作。
- 不能对转换中的表做除了DROP以外的任意DDL操作。

##### 1.6.3. NOLOGGING

该语句用于将表转为nologging属性。若对已是nologging属性的表执行该语句，直接返回成功。若对logging属性的表执行该语句，会将其转为nologging属性。建议只在数据迁移场景打开此属性。

执行该语句存在如下约束限制：

- 不能将临时表设置为nologging属性。
- 不能将存在UDT、内置UDT（例如XMLTYPE、ST_GEOMETRY、BOX2D）列的表设置为nologging属性。
- 主备环境中，不能将表设置为nologging属性。

nologging属性的表存在如下特征：

- nologging表对DML操作仅支持插入数据和导入数据，且插入数据时只会记录必要的redo和undo（例如segment相关的redo），其他redo会被忽略。
- nologging表不能执行并发操作。
- nologging表不能执行回滚操作。
- nologging表不能在线创建索引（[CREATE INDEX ONLINE](./CREATE INDEX)）和在线重建索引（[ALTER INDEX REBUILD ONLINE](./ALTER INDEX)）。
- 数据库重启时会将nologging表标记为corrupted，阻止对其进行除drop和truncate外的任何操作。
- 如果一个事务失败，该事务内所有执行过插入数据操作的nologging表都会被标记为corrupted。
- nologging属性的LSC表使用[bulkload模式](./LOAD DATA)导入数据时，不受nologging属性影响（性能无变化，失败也不会被标记为corrupted）。
- nologging属性的TAC表不允许修改列和删除主键约束。
- truncate nologging表，会将nologging表的属性修改为logging。
- 在共享集群部署中，如果发生实例启停、实例加入或共享集群故障，会把所有nologging表都标记为corrupted。
- 在共享集群部署中，设置表的nologging属性会绑定实例（可通过GV$DICT_CACHE视图的nologging_instance_id字段查看所绑定的实例ID），下列操作只能在被绑定的实例上执行：
    - 向nologging表插入数据。
    - 将表从nologging属性变更为logging。


示例（单机部署、共享集群部署）

```sql
-- 执行如下语句开启logging
ALTER TABLE area LOGGING;

-- 在主备环境中，将表设置为nologging会报错
ALTER TABLE area NOLOGGING;
YAS-02328 table nologging is not allowed when standby exists
```

<span id="parallelclause" name="parallelclause" class="yaslink"></span>

#### 1.7. parallel\_clause

该语句用于修改表的parallel属性，parallel属性用于指定查询该表的并行度，语法同CREATE TABLE中的[parallel_clause](../SQL语句（yashan模式）/CREATE TABLE.html#parallelclause)。

示例

```sql
-- 开启parallel并指定并行度为8
ALTER TABLE area PARALLEL 8;

-- 关闭parallel
ALTER TABLE area NOPARALLEL;
```

<span id="readonlyclause" name="readonlyclause" class="yaslink"></span>

#### 1.8. readonly\_clause

无实际含义。

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

### 2. alter\_column\_clause

该语句用于指定对表的列字段的操作。

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### 2.1. add\_column\_clause

该语句用于为表增加列字段，同时增加多项时在`()`中以`,`分隔。COLUMN关键字可省略。

<span id="columndefinition" name="columndefinition" class="yaslink"></span>

##### 2.1.1. column\_definition

为表增加一个新的列字段，并对增加的列字段进行数据类型（DataType）、缺省值（DEFAULT）、行内约束（inline_constraint）等定义。

对非空表增加定义了NOT NULL约束的列字段时，必须同时为其指定缺省值，否则将提示错误。

###### 2.1.1.1. DataType

指定列字段的数据类型。查看YashanDB的[数据类型描述](../数据类型（yashan模式）/00数据类型（yashan模式）)。

其中，数据类型指定为LOB/JSON类型时，可通过[lob_clauses](#lobclause)子句指定其存储属性。

YashanDB不允许将新增的列字段指定为Nested Table UDT类型。

###### 2.1.1.2. DEFAULT

为增加的列字段指定一个符合该列字段数据类型的缺省值，default_expr可以为字面量、运算式、函数等表达式。

当指定了缺省值时，系统将对表中现有行的该列字段填充该缺省值。

- 对于HEAP表的新增列字段，该列为XMLTYPE类型时不能指定缺省值。

- 对于LSC表的新增列字段，该列为LOB型时不能指定缺省值；为其他类型时，缺省值不能定义为[Sequence伪列](../基本SQL元素/伪列)。

示例（单机LSC表）

```sql
--创建LSC表lsc_forb_def
CREATE TABLE lsc_forb_def(c1 INT,c2 INT);

--LSC表中新增指定了默认值的lob列会报错
ALTER TABLE lsc_forb_def ADD(c3 CLOB DEFAULT 'default');
YAS-00004 feature "add lob column with default expr on LSC table" has not been implemented yet

--创建序列seq1
CREATE SEQUENCE seq1;

--LSC表中新增列的默认值为Sequence伪列时会报错
ALTER TABLE lsc_forb_def ADD(c3 INT DEFAULT seq1.NEXTVAL);
YAS-00004 feature "add column with default sequence on LSC table" has not been implemented yet

ALTER TABLE lsc_forb_def ADD(c3 INT DEFAULT seq1.currval);
YAS-00004 feature "add column with default sequence on LSC table" has not been implemented yet
```

###### 2.1.1.3. inline\_constraint

该语句用于在增加列字段时同时定义表的行内约束项。关于约束项的详细描述请参考通用SQL语法[constraint](../通用SQL语法/constraint（yashan模式）)。

示例

```sql
-- 为area表新增address非空字段
ALTER TABLE area ADD (address VARCHAR(30) NOT NULL DEFAULT 'no address');

SELECT area_no,address FROM area;
AREA_NO ADDRESS                           
------- --------------------------------- 
01      no address                       
02      no address                       
03      no address                       
04      no address                       
05      no address
```
<span id="columnencryptionclause" name="columnencryptionclause" class="yaslink"></span>
###### 2.1.1.4. column\_encryption\_clause

该语句用于在增加列字段时同时定义列的加密属性，规则同[CREATE TABLE](./CREATE TABLE.html#columnencryptionclause)。

示例（HEAP表、LSC表）

```sql
-- 在表中新增address列并加密
ALTER TABLE encrypt_col_employees ADD address VARCHAR(200) ENCRYPT USING 'AES128';
```

<span id="lobclause" name="lobclause" class="yaslink"></span>

##### 2.1.2. lob\_clause

该语句为增加的LOB/JSON字段指定存储属性，语法同[CREATE TABLE](./CREATE TABLE)中lob_clause子句描述。

<span id="dropcolumnclause" name="dropcolumnclause" class="yaslink"></span>

#### 2.2. drop\_column\_clause

该语句用于删除表中已有的列字段或约束项。

<span id="dropcolumn" name="dropcolumn" class="yaslink"></span>

##### 2.2.1. drop\_column

删除指定的列字段，指定多个列字段时用`,`分隔。COLUMN关键字可省略。

该语句遵循如下规则：

- 不允许删除表中的所有列字段。
- 如果删除的列字段已被定义为主键组合中的一项，则不允许删除此列，但可以删除整个主键对应的列组合。
- 如果删除的列字段已被定义为索引对象中的一项，则删除此列字段后，其对应的索引对象也被删除。
- 分布式部署中，不允许删除分区键所在的列字段。
- LSC表中，不允许删除被定义为排序键的列字段。
- 如果数据库后台正在执行回滚，则不允许删除TAC表/含有热数据的LSC表中使用变长存储的列。

示例（单机、共享集群部署）

```sql
-- 创建product_pri表
CREATE TABLE product_pri AS SELECT * FROM product;

-- 定义组合主键product_no+product_name
ALTER TABLE product_pri ADD PRIMARY KEY(product_no,product_name);

-- 创建索引
CREATE UNIQUE INDEX idx_product_pri_1 ON product_pri(cost,price);
   
-- 删除部分主键字段，返回错误
ALTER TABLE product_pri DROP COLUMN product_no;
YAS-02132 cannot drop column referenced in a multi-column constraint
   
-- 删除全部主键字段，成功
ALTER TABLE product_pri DROP COLUMN(product_no,product_name);

-- 删除部分索引字段，成功
ALTER TABLE product_pri DROP COLUMN cost;
   
-- 删除表中最后的一个字段，返回错误
ALTER TABLE product_pri DROP COLUMN price;
YAS-02054 cannot drop all columns in a table
```

示例（分布式TAC表）

```sql
-- 创建area_part表
CREATE TABLE area_part AS SELECT * FROM area;

-- 删除分区键字段，返回错误
ALTER TABLE area_part DROP COLUMN area_no;
YAS-02142 cannot drop partitioning column
   
-- 删除非分区键字段，成功
ALTER TABLE area_part DROP COLUMN area_name;
```

示例（LSC表）

```sql
--创建area_orderkey表，并指定排序键为area_no
CREATE TABLE area_orderkey AS SELECT * FROM area ORDER BY area_no;

--删除排序键所在字段，返回错误
ALTER TABLE area_orderkey DROP COLUMN area_no;
YAS-03726 cannot drop order key column
```

<span id="renamecolumnclause" name="renamecolumnclause" class="yaslink"></span>

#### 2.3. rename\_column\_clause

该语句用于对列字段进行重命名，指定的新名称不允许为空且必须符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

示例（HEAP表、TAC表）

```sql
ALTER TABLE branches RENAME branch_no TO branchno;
ALTER TABLE branches RENAME branchno TO branch_no;
```

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### 2.4. modify\_column\_clause

该语句用于对列字段的数据类型、默认值、约束等属性进行修改，同时修改多个列字段时需在`()`中以`,`分隔。

YashanDB支持同时对列字段修改多个属性，但需遵循与[CREATE TABLE](./CREATE TABLE)相同的顺序要求，否则系统提示错误；当同时修改多个约束项时，对这些约束项无顺序要求。

##### 2.4.1. dataType

修改列字段的数据类型为YashanDB认可的[数据类型](../数据类型（yashan模式）/00数据类型（yashan模式）)。

LSC表不允许修改列字段的数据类型，HEAP表和TAC表列字段数据类型修改规则如下：

- **外键约束所在列**：不允许修改子表和父表中相应列的数据类型。

- **索引所在列**：
  
    - 若为空表（即无数据），允许将其修改为除LOB/JSON/UDT/XMLTYPE外的数据类型。

    - 若为非空表，则不允许修改其数据类型。

- **使用字典编码的列（仅适用于TAC表）**：仅允许将其修改为字符型，但仍遵循字符型列的修改规则。

- **变长存储的字符型列（仅适用于TAC表）**：不允许在数据库进行回滚时，修改其数据类型。

- **分区键、函数索引或AC所在列**：不允许修改其数据类型。

- **原数据类型为LOB/JSON/UDT/XMLTYPE的其他列**：不允许修改其数据类型。

- **原数据类型为字符型的其他空列**：允许将其修改为除LOB/JSON/UDT/XMLTYPE外的其他数据类型。

- **原数据类型为字符型的其他非空列**：允许修改为不同字符型，但仍需遵顼以下规则：

    - 不允许CHAR/VARCHAR与NCHAR/NVARCHAR交叉修改。

    - 当按长度属性从大向小修改时，必须保证该列现有数据的长度均未超过目标数据类型的上限，否则无法修改成功并提示相应错误。

    - HEAP表不允许跨存储方式修改数据类型，例如HEAP表不允许将列数据类型从VARCHAR(3200)（采用普通字符串存储）修改为VARCHAR(8004)（会自动转换为LOB型存储），而TAC表允许此操作。存储方式介绍请查阅[字符型](../数据类型（yashan模式）/字符型)。

- **原数据类型为其他数据类型的其他空列**：允许将其修改为除LOB/JSON/UDT/XMLTYPE外的其他数据类型。
  
- **原数据类型为其他数据类型的其他非空列**：

    - 不允许跨大类修改数据类型，例如不允许将数值型修改为字符型。

    - 同一数据大类中，除字符型外其他类型只允许按值域、精度、长度等属性从小向大修改数据类型，例如不允许将INT修改为指定了精度的NUMBER，不允许将DATE修改为TIME。

##### 2.4.2. DEFAULT

修改或删除列字段的缺省值，default_expr可以为字面量、运算式、函数等表达式。

示例（HEAP表、TAC表）

```sql

-- 创建示例表并插入示例数据
DROP TABLE IF EXISTS area;


CREATE TABLE area
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) NOT NULL,
 owner VARCHAR2(20));


INSERT INTO area VALUES ('01','华东','Shanghai','');

INSERT INTO area VALUES ('02','华西','Chengdu','');

INSERT INTO area VALUES ('03','华南','Guangzhou','');

INSERT INTO area VALUES ('04','华北','Beijing','');

INSERT INTO area VALUES ('05','华中','Wuhan','');


SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      华东                                                          Shanghai
02      华西                                                          Chengdu
03      华南                                                          Guangzhou
04      华北                                                          Beijing
05      华中                                                          Wuhan


-- 修改owner列配置默认值为zq，插入示例数据并查看默认值已生效
ALTER TABLE area MODIFY owner DEFAULT 'zq';

INSERT INTO area VALUES ('06','华中','Changsha','cc');

INSERT INTO area VALUES ('07','华中','Hengyang','');

INSERT INTO area VALUES ('08','华中','Ezhou',DEFAULT);

SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      华东                                                          Shanghai
02      华西                                                          Chengdu
03      华南                                                          Guangzhou
04      华北                                                          Beijing
05      华中                                                          Wuhan
06      华中                                                          Changsha              cc
07      华中                                                          Hengyang
08      华中                                                          Ezhou                 zq


-- 删除owner列配置的默认值，插入示例数据并查看默认值已删除
ALTER TABLE area MODIFY owner DEFAULT null;


INSERT INTO area VALUES ('10','华中','Enshi','');


SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      华东                                                          Shanghai
02      华西                                                          Chengdu
03      华南                                                          Guangzhou
04      华北                                                          Beijing
05      华中                                                          Wuhan
06      华中                                                          Changsha              cc
07      华中                                                          Hengyang
08      华中                                                          Ezhou                 zq
10      华中                                                          Enshi


-- 删除表，清理测试数据

DROP TABLE IF EXISTS area;


```

##### 2.4.3. inline\_constraint

修改列字段的约束项，遵循通用[constraint](../通用SQL语法/constraint（yashan模式）)里的行内约束所定义语法要求。

###### 2.4.3.1. modify NULL/NOT NULL

修改NOT NULL约束项：

- 不允许对未定义NOT NULL约束项的列字段执行modify NULL。
- 不允许对已定义NOT NULL约束项的列字段执行modify NOT NULL。
- 不允许对已存在NULL数据的列字段执行modify NOT NULL。
- 不允许对已定义ON DELETE SET NULL或ON UPDATE SET NULL外键的列字段执行modify NOT NULL。

###### 2.4.3.2. modify UNIQUE 

修改UNIQUE约束项：

- 不允许对已定义UNIQUE约束项的列字段执行modify UNIQUE。
- 不允许对已存在重复数据的列字段执行modify UNIQUE。
- 不允许对多个列字段同时执行modify UNIQUE。

###### 2.4.3.3. modify PRIMARY KEY

修改主键约束项：

- 不允许对已有主键的表执行任意列字段的modify PRIMARY KEY。
- 不允许对已定义UNIQUE约束项的列字段执行modify PRIMARY KEY。
- 不允许对不符合创建主键要求的列字段执行modify PRIMARY KEY，创建主键的要求请参考[constraint](../通用SQL语法/constraint（yashan模式）)描述。

###### 2.4.3.4. modify CHECK

修改CHECK约束项：

- 如新的CHECK与表中现有其他的CHECK冲突，无法执行修改。
- 如表中现有的数据不符合CHECK，无法执行修改。

###### 2.4.3.5. modify FOREIGN KEY

修改外键约束项：

- 不允许对不符合创建外键要求的列字段执行modify FOREIGN KEY，创建外键的要求请参考[constraint](../通用SQL语法/constraint（yashan模式）)描述。

示例（HEAP表、TAC表）

```sql
--修改branches非空表的branch_name非空列字段数据类型，只能修改为字符型
ALTER TABLE branches MODIFY branch_name CHAR(400);

--修改branches表的branch_no和branch_name为UNIQUE，无法同时修改多个列字段为UNIQUE，返回错误
ALTER TABLE branches MODIFY (branch_no,branch_name) UNIQUE;
YAS-04297 invalid ALTER TABLE option

--修改branches表的address列字段的缺省值
ALTER TABLE branches MODIFY address DEFAULT 'no address';

--修改area表的area_name列字段为非空
ALTER TABLE area MODIFY area_name NOT NULL;
```

<span id="altertablepartition" name="altertablepartition" class="yaslink"></span>

### 3. alter\_table\_partition

该语句用于指定对表的分区的操作。

如在表上已建立了分区索引，当执行新增分区和删除分区的操作时，系统也会同时新增索引分区和删除索引分区。

分布式部署中不允许对一级分区执行本语句。

<span id="addtablepartition" name="addtablepartition" class="yaslink"></span>

#### 3.1. add\_table\_partition

该语句用于对表增加一个新的分区（Partition）。

不同类型的表可增加的分区类型不同：

- HEAP表支持增加范围（Range）、列表（List）和哈希（Hash）类型的分区。
- TAC表支持增加范围（Range）、列表（List）和哈希（Hash）类型的分区。
- LSC表支持增加范围（Range）和列表（List）类型的分区。

<span id="addrangepartitionclause" name="addrangepartitionclause" class="yaslink"></span>

##### 3.1.1. add\_range\_partition\_clause

增加范围类型的分区，只能在当前最大分区界值之上建立分区，如表的最大分区界值被设为MAXVALUE，则不允许增加分区。

定义了INTERVAL的范围分区表，其分区由系统自动维护，不可以用本语句为其增加分区。

<span id="tablepartitiondescription" name="tablepartitiondescription" class="yaslink"></span>

###### 3.1.1.1. table\_partition\_description

可以为新增的分区指定所属表空间（缺省为表所属表空间），及PCTFREE/PCTUSED/INITRANS/MAXTRANS等属性。

可以为新增的分区指定range、list或者hash子分区，子分区的类型需要和表的定义一致。

如建表时未指定该表为组合分区表，不允许通过本语句为新增分区指定子分区。

示例（单机、共享集群部署）

```sql
ALTER TABLE sales_info_range ADD PARTITION p_sales_info_range_4 VALUES LESS THAN('2038');

--创建一张range-list组合分区表range_list_table
CREATE TABLE range_list_table(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY LIST(b)
(PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1 VALUES(10), SUBPARTITION sp2 VALUES(20)),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3 VALUES(10), SUBPARTITION sp4 VALUES(20)));

--为其添加一个range分区并指定其list子分区
ALTER TABLE range_list_table ADD PARTITION p3 VALUES LESS THAN(3)(SUBPARTITION sp5 VALUES(10),SUBPARTITION sp6 VALUES(20));
```

<span id="addlistpartitionclause" name="addlistpartitionclause" class="yaslink"></span>

##### 3.1.2. add\_list\_partition\_clause

增加列表类型的分区，如表已在列表值里定义了DEFAULT，则不允许增加分区。

分区列表值可以指定为通用表达式（[expr](../通用SQL语法/expr)）。

###### 3.1.2.1. table\_partition\_description

可以为新增的分区指定所属表空间（缺省为表所属表空间），及PCTFREE/PCTUSED/INITRANS/MAXTRANS等属性。

可以为新增的分区指定range、list或者hash子分区，子分区的类型需要和表的定义一致。

如建表时未指定该表为组合分区表，不允许通过本语句为新增分区指定子分区。

当分区列数量为一并且新增分区包含多个值时，每个值需要用括号包起来，否则报错。

示例（单机、共享集群部署）

```sql
ALTER TABLE sales_info_list ADD PARTITION p_sales_info_list_3 VALUES ('2022');
ALTER TABLE sales_info_list ADD PARTITION p_sales_info_list_4 VALUES (TO_CHAR(2023));

--创建一张list-list组合表list_list_table
CREATE TABLE list_list_table(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY LIST(b)
(PARTITION p1 VALUES(1) (SUBPARTITION sp1 VALUES(1), SUBPARTITION sp2 VALUES(2)),
PARTITION p2 VALUES(2) (SUBPARTITION sp3 VALUES(1), SUBPARTITION sp4 VALUES(2)));

--为其添加一个list分区并指定其list子分区
ALTER TABLE list_list_table ADD PARTITION p3 VALUES (3)(SUBPARTITION sp5 VALUES(1), SUBPARTITION sp6 VALUES(2));
```

<span id="addhashpartitionclause" name="addhashpartitionclause" class="yaslink"></span>

##### 3.1.3. add\_hash\_partition\_clause

增加哈希类型的分区。本语句只适用于HEAP/TAC表。

新增分区的PCTFREE/PCTUSED/INITRANS/MAXTRANS等属性从表上继承，不允许指定。

可以为新增的分区指定range、list或者hash子分区，子分区的类型需要和表的定义一致。

如建表时未指定该表为组合分区表，不允许通过本语句为新增分区指定子分区。

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

###### 3.1.3.1. partition\_storage\_clause

可以为新增的分区指定所属表空间（缺省为表所属表空间）。

示例（HEAP表、单机TAC表）

```sql
ALTER TABLE sales_info_hash ADD PARTITION p_sales_info_hash_3;
ALTER TABLE sales_info_hash ADD PARTITION p_sales_info_hash_4;

--创建一张hash-list组合分区表hash_list_table
CREATE TABLE hash_list_table(c1 INT, c2 VARCHAR(10))
PARTITION BY HASH(c1)
SUBPARTITION BY LIST(c2)
(
PARTITION p1(SUBPARTITION sp1 VALUES('a')), 
PARTITION p2 (SUBPARTITION sp3 VALUES('d'), SUBPARTITION sp4 VALUES(DEFAULT))
);
--为其添加一个hash分区并指定其list子分区
ALTER TABLE hash_list_table ADD PARTITION p3(SUBPARTITION sp5 VALUES ('f'));
```

<span id="droptablepartition" name="droptablepartition" class="yaslink"></span>

#### 3.2. drop\_table\_partition

该语句用于删除表的分区，同时删除分区里的数据。指定多个分区用`,`分隔。

该语句指定的分区中存有数据时，会同步失效表上的全局索引。分布式部署模式下，不管指定的分区是否存有数据，都会失效表上的全局索引。

该语句存在如下约束限制：

- 删除分区时，不允许将表上的所有分区都删除。
- 不允许删除哈希类型分区。
- 在表的分区被删除时，对应的索引分区也会被删除。
- 对于范围类型分区，删除分区后其分区界值将会向相邻大一级分区合并，这样符合此被删除分区界值范围的新增表数据将进入相邻分区中，如不存在相邻大一级分区则数据无法再插入成功。

示例（单机、共享集群部署）

```sql
--sales_info_range为一张范围分区表
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON  
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0201   11001              30         500 0201010011 
2000  12    0102   11001              20         300            
2015  11    0101   11001              20         300            
2015  03    0102   11001              20         300            
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600       
   
--分区及数据被删除
ALTER TABLE sales_info_range DROP PARTITION p_sales_info_range_2;
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON  
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0201   11001              30         500 0201010011 
2000  12    0102   11001              20         300            
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600 
   
INSERT INTO sales_info_range VALUES ('2015','03','0101','11001',20,300,'');
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range PARTITION(p_sales_info_range_3);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON  
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600            
2015  03    0101   11001              20         300
```

<span id="updateindexclause" name="updateindexclause" class="yaslink"></span>

##### 3.2.1. update\_index\_clause

指定分区被删除后，对表上的全局索引（Global Index）的处理，默认为INVALIDATE。

*   INVALIDATE GLOBAL INDEXES：将全局索引全部失效，不可用。
*   UPDATE GLOBAL INDEXES：全局索引不失效仍然可用。

示例（单机、共享集群部署）

```sql
ALTER TABLE sales_info_list DROP PARTITION p_sales_info_list_4 UPDATE GLOBAL INDEXES;
```

<span id="droptablesubpartition" name="droptablesubpartition" class="yaslink"></span>

#### 3.3. drop\_table\_subpartition

该语句用于删除表的子分区，同时删除子分区里的数据。指定多个子分区用`,`分隔。

本语句存在如下约束限制：

- 删除子分区时，不允许将分区下的所有子分区都删除，也不允许跨分区删除子分区。
- 不允许删除哈希类型子分区。
- 在表的子分区被删除时，对应的索引子分区也会被删除。

示例

```sql
--获得sales_info表的子分区名称
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
--以下输出以单机为例
PARTITION_NAME               SUBPARTITION_NAME
---------------------------- ---------------------------------------
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_1
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_2
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_3
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_1
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_2
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_3
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_1
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_2
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_3

--选择其中一个子分区删除
ALTER TABLE sales_info DROP SUBPARTITION P_SALES_INFO_1_SP_SALES_INFO_1;
```

##### 3.3.1. update\_index\_clause

同drop_table_partition语句中描述一致。

<span id="truncatetablepartition" name="truncatetablepartition" class="yaslink"></span>

#### 3.4. truncate\_table\_partition

该语句用于删除分区里的所有数据，指定删除多个分区数据用`,`分隔。

该语句同时删除分区对应的本地索引数据（Local Index）。

该语句指定的分区中存有数据时，会同步失效表上的全局索引。分布式部署模式下，不管指定的分区是否存有数据，都会失效表上的全局索引。

<span id="truncatepartclause" name="truncatepartclause" class="yaslink"></span>

##### 3.4.1. truncate\_part\_clause

该语句用于指定删除分区数据的方式。

###### 3.4.1.1. DROP|REUSE STORAGE

指定删除分区数据后释放|保留其存储空间。

###### 3.4.1.2. CASCADE

如某张分区表为被子表定义了外键约束的父表，且要删除的分区数据在子表对应列字段值中已经存在，则指定CASCADE将同时删除子表中对应的数据行。本语句只作用于HEAP表。

###### 3.4.1.3. PURGE

当回收站开启时，被删除的数据默认将进入回收站，指定本关键字则表示数据被彻底删除，不进入回收站。本语句只作用于HEAP表。

示例（单机、共享集群部署）

```sql
--获得sales_info表的分区名称
SELECT partition_name
FROM DBA_TAB_PARTITIONS
WHERE table_name='SALES_INFO';
--以下输出以单机为例
PARTITION_NAME
----------------------------------------------------------------
P_SALES_INFO_1
P_SALES_INFO_2
P_SALES_INFO_3

ALTER TABLE sales_info TRUNCATE PARTITION P_SALES_INFO_1 DROP STORAGE;
```

<span id="truncatetablesubpartition" name="truncatetablesubpartition" class="yaslink"></span>

#### 3.5. truncate\_table\_subpartition

该语句用于删除子分区里的所有数据，指定删除多个子分区数据用`,`分隔。

该语句同时删除子分区对应的本地索引数据（Local Index）。

##### 3.5.1. truncate\_part\_clause

同truncate_table_partition语句中描述一致。

示例（单机、共享集群部署）

```sql
ALTER TABLE sales_info TRUNCATE SUBPARTITION P_SALES_INFO_3_SP_SALES_INFO_1 DROP STORAGE;
```

<span id="setpartitionclause" name="setpartitionclause" class="yaslink"></span>

#### 3.6. set\_partition\_clause

该语句只作用于HEAP表，且只针对范围分区表，用于指定INTERVAL和非INTERVAL分区类型之间的相互转换。转换分区类型不会影响表中原有的数据，新增加的数据则按转换后的分区类型特点存储。

分布式部署中用户无法执行此操作。

##### 3.6.1. transfer range to range-interval

将RANGE分区表转换为INTERVAL分区时，要求分区键的数据类型为number或date，语法如下：

```sql
ALTER TABLE table_name SET INTERVAL(expr);
```

建立INTERVAL分区的要求和规则与[CREATE TABLE](./CREATE TABLE)里的interval_clause中描述一致，不符合时无法成功转换。

示例（HEAP表）

```sql
--创建示例表sales_info_range1，与样例表中sales_info_range区别仅在于分区键year的数据类型为int
CREATE TABLE sales_info_range1
(year INT NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4),
product CHAR(5),
quantity NUMBER DEFAULT 0 NOT NULL,
amount NUMBER(10,2) DEFAULT 0 NOT NULL,
salsperson CHAR(10))
PARTITION BY RANGE(year)
(PARTITION p_sales_info_range_1 VALUES LESS THAN(2011),
PARTITION p_sales_info_range_2 VALUES LESS THAN(2021),
PARTITION p_sales_info_range_3 VALUES LESS THAN(2031));
--将sales_info_range1表的分区类型转换为INTERVAL类型
ALTER TABLE sales_info_range1 SET INTERVAL(2);

--对于超过现有分区界值的数据，系统将创建新分区且类型为INTERVAL
INSERT INTO sales_info_range1 VALUES ('2031','05','0101','11001',40,600,'');
COMMIT;
SELECT partition_name,tablespace_name,high_value
FROM USER_TAB_PARTITIONS 
WHERE table_name='SALES_INFO_RANGE1'
ORDER BY partition_name;
PARTITION_NAME          TABLESPACE_NAME  HIGH_VALUE  
----------------------- ---------------- ----------- 
P_SALES_INFO_RANGE_1    USERS            2011     
P_SALES_INFO_RANGE_2    USERS            2021     
P_SALES_INFO_RANGE_3    USERS            2031     
SYS_P21                 USERS            2033       
```

##### 3.6.2. modify range-interval

修改INTERVAL分区的属性。

###### 3.6.2.1. 修改INTERVAL值

调整INTERVAL为新的值，之后插入的数据将按新值创建分区。

本功能语法为：

```sql
ALTER TABLE table_name SET INTERVAL(expr);
```

相关要求和规则与上述transfer range to range-interval一致。

###### 3.6.2.2. 修改分区表空间

指定INTERVAL分区的表空间，指定多个以`,`分隔，之后所有新创建的分区将循环使用这些表空间。

本功能语法为：

```sql
ALTER TABLE table_name SET STORE IN(tablespace_name, ...);
```

只能对INTERVAL类型的范围分区表执行此操作，否则系统报错。

示例（HEAP表）

```sql
--为sales_info_range1表新建两个表空间
CREATE TABLESPACE sales_tb1;
CREATE TABLESPACE sales_tb2;

--对上例中的INTERVAL分区表修改INTERVAL和表空间
ALTER TABLE sales_info_range1 SET INTERVAL(3);
ALTER TABLE sales_info_range1 SET STORE IN(sales_tb1,sales_tb2);
INSERT INTO sales_info_range1 VALUES ('2033','05','0101','11001',40,600,'');
INSERT INTO sales_info_range1 VALUES ('2043','05','0101','11001',40,600,'');
COMMIT;
SELECT partition_name,tablespace_name,high_value
FROM USER_TAB_PARTITIONS 
WHERE table_name='SALES_INFO_RANGE1'
ORDER BY partition_name;
PARTITION_NAME          TABLESPACE_NAME      HIGH_VALUE  
----------------------- -------------------- ----------- 
P_SALES_INFO_RANGE_1    USERS                2011     
P_SALES_INFO_RANGE_2    USERS                2021     
P_SALES_INFO_RANGE_3    USERS                2031     
SYS_P21                 USERS                2033       
SYS_P22                 SALES_TB1            2036       
SYS_P23                 SALES_TB2            2045  
```

##### 3.6.3. transfer range-interval to range

将INTERVAL范围分区转换为非INTERVAL的范围分区，语法如下：

```sql
ALTER TABLE table_name SET INTERVAL();
```

示例（HEAP表）

```sql
--将上例中的INTERVAL分区表修改为非INTERVAL的范围分区表
ALTER TABLE sales_info_range1 SET INTERVAL();
```

<span id="modifypartitionclause" name="modifypartitionclause" class="yaslink"></span>

#### 3.7. modify\_partition\_clause

该语句用于对表分区进行操作，对于一级分区其中包含收缩操作和添加二级分区的操作，对于二级分区只有收缩操作。

##### 3.7.1. shrink\_space\_clause

收缩操作与表的收缩一致，请参考[shrink_space_clause](#shrinkspaceclause)。

示例（HEAP表）

```sql
ALTER TABLE orders_info ENABLE ROW MOVEMENT;
ALTER TABLE sales_info ENABLE ROW MOVEMENT;

ALTER TABLE orders_info MODIFY PARTITION p_orders_info_1 SHRINK SPACE COMPACT CASCADE;
ALTER TABLE sales_info MODIFY SUBPARTITION P_SALES_INFO_3_SP_SALES_INFO_1 SHRINK SPACE COMPACT CASCADE;
```
<span id="addsubpartitionclause" name="addsubpartitionclause" class="yaslink"></span>

##### 3.7.2. add\_subpartition\_clause

该语句用于在组合分区表中给指定的一级分区添加二级分区。

该语句中range_subpartition_desc、list_subpartition_desc、individual_hash_subparts的描述请参考[CREATE TABLE](./CREATE TABLE)。

示例（HEAP表、单机TAC表）

```sql
CREATE TABLE composite_table(c1 INT, c2 INT)
PARTITION BY RANGE(c1)
SUBPARTITION BY HASH(c2)
(
PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1, SUBPARTITION sp2),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3, SUBPARTITION sp4)
);

ALTER TABLE composite_table MODIFY PARTITION p1 ADD SUBPARTITION p1_subp1;

-- 表创建时未指定为组合分区表，执行本语句会返回错误
CREATE TABLE partition_table(c1 INT,c2 INT)
PARTITION BY RANGE(c1)
INTERVAL (10)
(PARTITION par1 VALUES LESS THAN(100));

ALTER TABLE partition_table MODIFY PARTITION par1 ADD SUBPARTITION par1_subp1;
YAS-02374 table is not partitioned by composite partition method
```

<span id="splittablepartition" name="splittablepartition" class="yaslink"></span>

#### 3.8. split\_table\_partition

该语句能将一个范围或者列表分区重新划分为多个分区。当一个分区过于臃肿影响到了查询、备份等性能时，可以考虑SPLIT PARTITION。

只能对HEAP表和单机TAC表执行本语句。

该语句须遵循如下限制：

- 不可对组合分区表执行SPLIT PARTITION。
- SPLIT PARTITION后默认会失效全局索引，请SPLIT后[重建（rebuild_clause）](./ALTER INDEX)全局索引，或者指定UPDATE GLOBAL INDEXES。
- 若指定UPDATE INDEXES，则SPLIT PARTITION后不会失效LOCAL索引和全局索引。
- 未指定分区名的表分区、索引分区、LOB分区会按照SYS_Pn的格式自动生成分区名。

##### 3.8.1. SPLIT AT (literal) INTO

该语句根据指定的字面量将一个范围分区划分为两个范围分区，仅适用于范围分区。

###### 3.8.1.1. literal

划分值，该值须位于被划分的分区范围内，否则返回错误。

###### 3.8.1.2. range\_partition\_desc

该语句用于指定划分后两个范围分区的名称，其中一个分区名可保留原分区名称，另一分区名不可与已有的分区名称重复。

新范围分区上下限分别为`[原范围分区下限，literal]`和`[literal，原范围分区上限]`。

示例（HEAP表、单机TAC表）

```sql
-- split range分区
DROP TABLE IF EXISTS split_range_part;
CREATE TABLE split_range_part(c1 INT)
PARTITION BY RANGE(c1)
(PARTITION p1 VALUES LESS than(100),
PARTITION p2 VALUES LESS than(200),
PARTITION p3 VALUES LESS than(300),
PARTITION p4 VALUES LESS than(MAXVALUE));
-- 将p4 split出p4和p5两个分区，新p4的分区边界值为350，p5的分区边界值为maxvalue
ALTER TABLE split_range_part split PARTITION p4 at(350) INTO (PARTITION p4, PARTITION p5);
```

##### 3.8.2. SPLIT VALUES (list\_values) INTO

该语句根据指定的列表内容将一个列表分区划分为两个列表分区，仅适用于list分区。

###### 3.8.2.1. list\_values

划分的列表内容，其中的值须包含在被划分的列表分区内，否则返回错误。

###### 3.8.2.2. list\_partition\_desc

该语句用于指定划分后两个列表分区的名称，其中一个分区名可保留原分区名称，另一分区名不可与已有的分区名称重复。

划分后的首个列表分区包含list_values中所有值，其余值保存在第二个列表分区中。

示例（HEAP表、单机TAC表）

```sql
-- split list分区
DROP TABLE IF EXISTS split_list_part;
CREATE TABLE split_list_part(c1 INT)
PARTITION BY LIST(c1)
(PARTITION p1 VALUES (100, 150, 170),
PARTITION p2 VALUES (200, 250, 280),
PARTITION p3 VALUES (300, 400),
PARTITION p4 VALUES (DEFAULT));
-- 将p4 split出p4和p5两个分区，新p4分区仅包含值350，p5分区包含default中除350之外所有值
ALTER TABLE split_list_part split PARTITION p4 VALUES(350) INTO (PARTITION p4, PARTITION p5);
```

##### 3.8.3. SPLIT INTO (range/list\_partition\_desc) 

该语句可将范围分区或列表分区根据指定内容划分为多个分区，适用于范围分区和列表分区。

###### 3.8.3.1. range\_partition\_desc|list\_partition\_desc

该语句用于指定划分后的分区具体信息，包括分区名称、类型和分区值，新分区类型须与原分区类型保持一致，多个分区间使用`,`进行分隔。

划分后的其中一个分区名可保留原分区名称，其余分区名不可与已有的分区名称重复。

###### 3.8.3.2. Partition partname table\_partition\_description

该语句用于指定包含所有剩余值的分区名称及信息，名称不可与已有分区的名称重复。

示例（HEAP表、单机TAC表）

```sql
-- split list分区
DROP TABLE IF EXISTS split_list_part;
CREATE TABLE split_list_part(c1 INT)
PARTITION BY LIST(c1)
(PARTITION p1 VALUES (100, 150, 170),
PARTITION p2 VALUES (200, 250, 280),
PARTITION p3 VALUES (300, 400),
PARTITION p4 VALUES (DEFAULT));
-- 使用split into语法
ALTER TABLE split_list_part split PARTITION p4 INTO (PARTITION p4 VALUES(350), PARTITION p5);

-- split range分区
DROP TABLE IF EXISTS split_range_part;
CREATE TABLE split_range_part(c1 INT)
PARTITION BY RANGE(c1)
(PARTITION p1 VALUES LESS than(100),
PARTITION p2 VALUES LESS than(200),
PARTITION p3 VALUES LESS than(300),
PARTITION p4 VALUES LESS than(MAXVALUE));
-- 使用split into语法
ALTER TABLE split_range_part split PARTITION p4 INTO (PARTITION p4 VALUES LESS than(350), PARTITION p5);
```

<span id="mergetablepartitions" name="mergetablepartitions" class="yaslink"></span>

#### 3.9. merge\_table\_partitions

该语句用于在分区表中将指定的多个一级分区合并为一个分区。

只能对HEAP表执行本语句，且不能合并Hash分区。

该语句须遵循如下限制：

- range分区合并后，结果分区的大边界值将继承所有原分区中最大的分区边界值，小边界值将继承所有原分区中最小的分区边界值。
- list分区合并后，结果分区的数值列表将是所有原分区的数值列表的并集。如果原分区中存在DEFAULT list分区，则结果分区为DEFAULT list分区。
- 当合并组合分区时，不能指定结果分区的二级分区属性，系统自动根据该组合分区表的二级分区模板生成二级分区；若没有二级分区模板，系统自动为新的一级range分区创建一个MAXVALUE二级分区，为新的一级list分区自动创建一个DEFAULT二级分区。

<span id="partitionorkeyvalue" name="partitionorkeyvalue" class="yaslink"></span>
##### 3.9.1. partition\_or\_key\_value

用于指定待合并的分区。

如需合并range分区，须按照分区边界从小到大指定连续的分区集（即彼此应为相邻的分区）。

##### 3.9.2. table\_partition\_description

可以按需为合并后的新分区指定所属表空间（缺省为表所属表空间）以及PCTFREE/PCTUSED/INITRANS/MAXTRANS等属性，可省略。

##### 3.9.3. UPDATE [GLOBAL] INDEXES

可以指定合并分区时是否同时更新索引，可省略，省略表示合并时不对索引进行更新。

- 指定UPDATE INDEXES，表示MERGE PARTITION同时更新LOCAL索引，合并分区后LOCAL索引不失效，全局索引也不会失效。
- 在原分区存在数据的情况下，MERGE PARTITION后会失效GLOBAL索引，需手动[重建（rebuild_clause）](./ALTER INDEX.html#rebuildclause)GLOBAL索引，或者指定UPDATE [GLOBAL] INDEXES不让索引失效。若原分区不存在数据，MERGE PARTITION不会改变GLOBAL索引的状态。

示例（HEAP表）

```sql
DROP TABLE IF EXISTS students;
CREATE TABLE students(id INT,name VARCHAR(10)) PARTITION BY RANGE(id)(
    PARTITION p1 VALUES LESS than(10) TABLESPACE users,
    PARTITION p2 VALUES LESS than(20) TABLESPACE users,
    PARTITION p3 VALUES LESS than(30));
-- 将p2，p3合并成p3，p3的分区边界值不变
ALTER TABLE students MERGE partitions p2,p3 INTO PARTITION p3;

DROP TABLE IF EXISTS students;
CREATE TABLE students(id INT,name VARCHAR(10)) PARTITION BY RANGE(id)(
    PARTITION p1 VALUES LESS than(10) TABLESPACE users,
    PARTITION p2 VALUES LESS than(20) TABLESPACE users,
    PARTITION p3 VALUES LESS than(30));
-- 将p2，p3合并成p4，p4的分区边界值与原p3一样
ALTER TABLE students MERGE partitions p2,p3 INTO PARTITION p4;

DROP TABLE IF EXISTS courses;
CREATE TABLE courses(id INT,name VARCHAR(10)) PARTITION BY LIST(id)(
    PARTITION p1 VALUES(1,2),
    PARTITION p2 VALUES(3,4),
    PARTITION p3 VALUES(5,6),
    PARTITION p4 VALUES(7,8)
);
-- 将p3，p1，p2和并成p1，p1的列表值为原p3，p1，p2的结合，即（1，2，3，4，5，6）
ALTER TABLE courses MERGE partitions p3,p1,p2 INTO PARTITION p1;
```

<span id="mergetablesubpartitions" name="mergetablesubpartitions" class="yaslink"></span>

#### 3.10. merge\_table\_subpartitions

该语句用于在组合分区表中将同一个一级分区下的多个二级分区合并为一个二级分区。

该语句的使用规则同[merge_table_partitions](#mergetablepartitions)。

<span id="subpartitionorkeyvalue" name="subpartitionorkeyvalue" class="yaslink"></span>
##### 3.10.1. subpartition\_or\_key\_value

用于指定待合并的二级分区。

如需合并range分区，须按照分区边界从小到大指定连续的分区集（即彼此应为相邻的分区）。

示例（HEAP表）

```sql
CREATE TABLE merge_rr_composite ( col1 INT,col2 SMALLINT,col3 INT)
PARTITION BY RANGE(col1) SUBPARTITION BY RANGE(col3)
(PARTITION p1 VALUES LESS than (301)(SUBPARTITION sp11 VALUES LESS than (4000) , SUBPARTITION sp12 VALUES LESS than (8000) ,SUBPARTITION sp13 VALUES LESS than (12001)),
 PARTITION p2 VALUES LESS than (601)(SUBPARTITION sp21 VALUES LESS than (4000) , SUBPARTITION sp22 VALUES LESS than (8000) ,SUBPARTITION sp23 VALUES LESS than (12001)),
 PARTITION p3 VALUES LESS than (801)(SUBPARTITION sp31 VALUES LESS than (4000) , SUBPARTITION sp32 VALUES LESS than (8000) ,SUBPARTITION sp33 VALUES LESS than (12001))
);
-- 合并sp11，sp12为sp12
ALTER TABLE merge_rr_composite MERGE subpartitions sp11 TO sp12 INTO SUBPARTITION sp12;

DROP TABLE IF EXISTS merge_rl;
CREATE TABLE merge_rl_composite(a INT, b INT, c CLOB)
PARTITION BY RANGE(a)
SUBPARTITION BY LIST(b) 
(
	PARTITION p1 VALUES LESS than(10)
	(
        SUBPARTITION sp3 VALUES(DEFAULT)
	),
	PARTITION p2 VALUES LESS than(20)
	(
		SUBPARTITION sp4 VALUES (2),
		SUBPARTITION sp5 VALUES (3)
	),
	PARTITION p3 VALUES LESS than(40)
	(
		SUBPARTITION sp7 VALUES (20)
	)
);
-- 合并sp4，sp5为sp10
ALTER TABLE merge_rl_composite MERGE subpartitions sp4, sp5 INTO SUBPARTITION sp10;
```

<span id="altertableconstraint" name="altertableconstraint" class="yaslink"></span>

### 4. alter\_table\_constraint

该语句用于指定对表上的约束的操作包括对添加约束、删除约束、修改约束和停用或启用约束。

<span id="addconstraint" name="addconstraint" class="yaslink"></span>

#### 4.1. add\_constraint

该语句用于添加约束，同时增加多项在`()`中以`,`分隔。

##### 4.1.1. out\_of\_line\_constraint

约束项，关于约束项的详细描述请参考通用SQL语法[constraint](../通用SQL语法/constraint（yashan模式）)。

<span id="dropconstraint" name="dropconstraint" class="yaslink"></span>

#### 4.2. drop\_constraint

该语句用于删除约束项。

如某个约束项在其创建时未指定名称，可以从系统提供视图（如USER_CONSTRAINTS）中查询到其默认名称后再执行删除操作。

删除UNIQUE/PRIMARY KEY约束项会自动删除对应的索引。

示例（HEAP表、）

```sql
ALTER TABLE branches DROP CONSTRAINT c_branches_1;

-- 存在外键时无法删除主键约束项
ALTER TABLE department DROP PRIMARY KEY;
YAS-02188 this unique/primary key is referenced by some foreign keys
```

<span id="modifyconstraint" name="modifyconstrain" class="yaslink"></span>

#### 4.3. modify\_constraint

该语句用于修改约束项的属性。

##### 4.3.1. 约束项名称

指定用于修改属性的约束项，可按如下三种方式指定（指定的约束项不存在时报错）：

- 对于主键约束项，以PRIMARY KEY表示即可。
- 对于唯一约束项，以UNIQUE表示即可，但需同时指定该约束项对应的列。
- 以CONSTRAINT 约束项名称表示，从系统视图（例如USER_CONSTRAINTS）可获得表上所有的约束项名称。

##### 4.3.2. ENABLE|DISABLE

启用或停用指定的约束项（含义见[constraint](../通用SQL语法/constraint（yashan模式）)中的ENABLE|DISABLE描述）。

对于存在子表外键关联的约束项，指定DISABLE将无法成功停用，除非同时指定CASCADE。

在指定ENABLE启用约束项时，如表中现有数据无法满足约束规则，则启用失败，除非同时指定NOVALIDATE（但对于主键/唯一/NOT NULL约束项无效）。

##### 4.3.3. VALIDATE|NOVALIDATE

启用或停用约束检查（含义见[constraint](../通用SQL语法/constraint（yashan模式）)中的VALIDATE|NOVALIDATE描述）。

本关键字可省略，则对ENABLE操作默认为VALIDATE，对DISABLE操作默认为NOVALIDATE。

##### 4.3.4. CASCADE

指定CASCADE表示在DISABLE一个存在子表外键关联的约束项时，同时对子表的外键约束项执行DISABLE。

需注意的是，在重新ENABLE父表的该约束项时，即使指定CASCADE也不会对子表的外键约束项执行ENABLE，用户需手工操作。

本关键字可省略，则在DISABLE一个存在子表外键关联的约束项时默认不会对子表的外键约束项执行DISABLE，而是进行错误提示。

示例

```sql
-- 停用area表的主键约束，同时停用子表上的外键约束
ALTER TABLE area MODIFY PRIMARY KEY DISABLE CASCADE;
ALTER TABLE area MODIFY PRIMARY KEY ENABLE;

-- 在branches表上创建不启用的唯一约束，之后启用
ALTER TABLE branches ADD UNIQUE(branch_no, area_no) DISABLE;
ALTER TABLE branches MODIFY UNIQUE(branch_no, area_no) ENABLE;
```

示例（HEAP表）

```sql
-- 停用branches表的area_no外键约束
ALTER TABLE branches ADD CONSTRAINT c_branches_1 
FOREIGN KEY (area_no) REFERENCES area(area_no) ON DELETE SET NULL;
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 DISABLE;
-- 修改area_no为在area表中不存在的值
UPDATE branches SET area_no='99' WHERE area_no='01';
COMMIT;
-- 启用branches表的area_no外键约束，但不启用约束检查，则可以启用成功
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 ENABLE NOVALIDATE;
```

<span id="enabledisableconstraint" name="enabledisableconstraint" class="yaslink"></span>

#### 4.4. enable\_disable\_constraint

该语句用于启用或者停用表上的某个约束项，拥有与[modify_constraint](#modifyconstraint)子句除MODIFY外相同的关键字和含义，但顺序不相同。

示例（HEAP表）

```sql
-- modify_constraint中的如下例句：
ALTER TABLE area MODIFY PRIMARY KEY DISABLE CASCADE;
ALTER TABLE area MODIFY PRIMARY KEY ENABLE;
ALTER TABLE branches MODIFY UNIQUE(branch_no, area_no) ENABLE;
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 ENABLE NOVALIDATE;

-- 在enable_disable_constraint中可以如下表示：
ALTER TABLE area DISABLE PRIMARY KEY CASCADE;
ALTER TABLE area ENABLE PRIMARY KEY;
ALTER TABLE branches ENABLE UNIQUE(branch_no, area_no);
ALTER TABLE branches ENABLE NOVALIDATE CONSTRAINT c_branches_1;
```

同时，本语句还提供如下与索引相关的语法选项（针对主键/唯一约束项）：

- using_index_clause

- (KEEP|DROP) INDEX

默认情况下，停用主键/唯一约束项会自动删除对应的索引，启用主键/唯一约束项会自动复用或者创建索引，上述两个选项用于对此情况进行人工干预。

##### 4.4.1. using\_index\_clause

在启用主键/唯一约束项时，使用本语句可对系统自动复用或创建的索引进行人工指定，详细语法定义和描述见[constraint](../通用SQL语法/constraint（yashan模式）)中的using_index_clause。

##### 4.4.2. (KEEP|DROP) INDEX

在停用主键/唯一约束项时，使用KEEP INDEX可指定不删除对应的索引，使用DROP INDEX则同默认情况，即删除对应的索引。

示例

```sql
-- 停用主键但保留对应索引
ALTER TABLE area DISABLE PRIMARY KEY CASCADE KEEP INDEX;

-- 停用主键同时删除对应索引
ALTER TABLE area DISABLE PRIMARY KEY CASCADE DROP INDEX;
```

<span id="reclaimtableclause" name="reclaimtableclause" class="yaslink"></span>

### 5. reclaim\_table\_clause

该语句用于在表空间迁移后，为迁移的对象声明存储对象和属性。

> **Warn**:
>
> 通常用于在分布式部署中进行节点/节点组扩缩容后，将逻辑对象与物理存储对象进行关联。
>
> 在该场景下，数据库会自动生成reclaim语句并自动在目标节点执行。**不建议用户手动执行该语句**，若不避免可能会导致系统崩溃或其他不可预知的结果。
