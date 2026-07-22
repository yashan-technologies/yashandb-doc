## 通用描述

CREATE TABLE语句用于创建一个表对象，表类型可分为HEAP表、TAC表和LSC表。存算一体分布式集群部署中，本语句还可以指定表对象的分布类型：[分布表](#shardtable)（Sharded Table）或[复制表](#duplicatetable)（Duplicated Table），默认为分布表。

YashanDB支持通过配置DEFAULT_TABLE_TYPE参数（HEAP|TAC|LSC）指定创建表对象时的默认表类型，且该参数允许在线切换。同时，支持通过[ORGANIZATION](#organizationclause)语法在创建表对象时指定其表类型。表对象创建成功后无法再修改表类型。

|  类型| 存储方式| 存储结构| 适用部署形态|
| ------------------ | -------- | ---------- | -------------------------------------- |
| HEAP表                                 | 行存     | 段页式结构 | <li>单机部署</li><li>共享集群/分布式集群部署</li><li>存算一体分布式集群部署</li>  |
| LSC表   | 列存     | 列存结构   | <li>单机部署</li><li>存算一体分布式集群部署</li>   |
| TAC表 | 列存     | 段页式结构 | <li>单机部署</li><li>存算一体分布式集群部署</li>   |

在分析业务场景中，推荐使用LSC表。

创建LSC表前，需确保其所在表空间已挂载bucket（数据桶）。通过[CREATE USER](./CREATE USER)语句创建的普通用户默认所属表空间会默认挂载bucket，因此普通用户可直接创建LSC表，而系统用户则因产品架构而异：

- 单机部署中，由于SYSTEM表空间默认未挂载bucket，系统用户（如SYS）无法直接创建LSC表，需先执行[ALTER TABLESPACE](./ALTER TABLESPACE)语句为SYSTEM表空间挂载bucket再创建LSC表。
- 存算一体分布式集群部署中，由于系统用户创建的表对象默认在users表空间中且users表空间默认已挂载bucket，无需额外操作即可直接创建LSC表。

> **Note**: 
>
> 在实际生产环境中，建议在创建业务表前先为其合理规划表空间和用户，相关语句请查阅[CREATE TABLESPACE](./CREATE TABLESPACE)和[CREATE USER](./CREATE USER)。

## 语句定义

**create table::=**

```ebnf
= CREATE [(GLOBAL|PRIVATE) TEMPORARY|SHARDED|DUPLICATED] TABLE [IF NOT EXISTS] [schema "."] table_name ( 
( "("  relation_properties ")"
[table_properties]
[lsc_table_properties]
[row_movement_clause] ) 
| object_table ).
```

**[relation\_properties](#relationproperties)::=**

```ebnf
= (column_definition|out_of_line_constraint) 
{"," (column_definition|out_of_line_constraint)}.
```

**[object\_table](#objecttable)::=**

```ebnf
= OF [schema "." object_type].
```

**[out\_of\_line\_constraint定义](../通用SQL语法/constraint)**

**[column\_definition](#columndefinition)::=**

```ebnf
= column_name dataType [VISIBLE|INVISIBLE] [identity_clause] [(DEFAULT default_expr|codec_expr|inline_constraint)
{" " (DEFAULT default_expr|codec_expr|inline_constraint)}].
```

**[identity_clause](#identityclause)::=**

```ebnf
= GENERATED [ALWAYS |(BY DEFAULT [(ON NULL)])] AS IDENTITY [identity_options].
```

**[identity_options](#identityoptions)::=**

```ebnf
= ["("] [(((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE)) 
{" " (((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE))}] [")"] .
```

**[codec_expr](#codecexpr)::=**

```ebnf
= [compression_clause] [encoding_clause] [column_encryption_clause].
```

**[compression_clause](#compressionclause)::=**

```ebnf
= COMPRESSION (compression_type (HIGH|MEDIUM|LOW) | UNCOMPRESSED).
```

**[compression_type](#compressiontype)::=**

```ebnf
= (LZ4|ZSTD).
```

**[encoding_clause](#encodingclause)::=**

```ebnf
= ENCODING (PLAIN|RLE|DICTIONARY'('(RLE|(PLAIN ["," CARDINALITY]))')'|"BYTE-PACKED").
```

**[column_encryption_clause](#columnencryptionclause)::=**

```ebnf
= ENCRYPT [USING  encryption_algorithm].
```

**[inline_constraint定义](../通用SQL语法/constraint)**

**[table\_properties](#tableproperties)::=**

```ebnf
= (organization_clause
|temp_table_attr_clause
|physical_attribute_clause
|table_partition_clause
|lob_clauses
|logging_clause
|parallel_clause
|cache_clause|readonly_clause|inmemory_clause
|table_compression
|nested_table_clauses
|table_encryption_clause
|object_affinity_clause) 
{" " (organization_clause
|temp_table_attr_clause
|physical_attribute_clause
|table_partition_clause
|lob_clauses
|logging_clause
|parallel_clause
|cache_clause|readonly_clause|inmemory_clause
|table_compression
|nested_table_clauses
|table_encryption_clause
|object_affinity_clause) }.
```

**[organization_clause](#organizationclause)::=**

```ebnf
= ORGANIZATION (HEAP|TAC|LSC|EXTERNAL external_table_clause).
```

**[external_table_clause定义](../通用SQL语法/external_table)**

**[temp\_table\_attr\_clause](#temptableattrclause)::=**

```ebnf
= ON COMMIT (((DROP|PRESERVE) DEFINITION)|((DELETE|PRESERVE) ROWS)) .
```

**[physical\_attribute\_clause](#physicalattributeclause)::=**

```ebnf
= ((TABLESPACE tablespace|TABLESPACE SET tablespace_set)|PCTFREE integer|PCTUSED integer|INITRANS integer|MAXTRANS integer|storage_clause|deferred_segment_creation).
```

**[storage_clause定义](../通用SQL语法/storage_clause)**

**[deferred\_segment\_creation](#deferredsegmentcreation)::=**

```ebnf
= SEGMENT CREATION (IMMEDIATE | DEFERRED) .
```

**[table\_partition\_clause](#tablepartitionclause)::=**

```ebnf
= (range_partitions
|list_partitions
|hash_partitions
|composite_range_partitions
|composite_list_partitions
|composite_hash_partitions
|consistent_hash_partitions
|consistent_hash_with_subpartitions).
```

**[range_partitions](#rangepartitions)::=**

```ebnf
= PARTITION BY RANGE "(" (column {"," column}) ")" [interval_clause] 
"(" (PARTITION [partname]  range_values_clause  [table_partition_description | lob_clauses])
{"," (PARTITION [partname]  range_values_clause  [table_partition_description | lob_clauses])} ")".
```

**[interval_clause](#intervalclause)::=**

```ebnf
= INTERVAL "(" expr ")" [STORE IN "(" (tablespace {"," tablespace}) ")"].
```

**[range\_values\_clause](#rangevaluesclause)::=**

```ebnf
= VALUES LESS THAN "(" (literal|MAXVALUE) {"," (literal|MAXVALUE)} ")".
```

**[table\_partition\_description](#tablepartitiondescription)::=**

```ebnf
= (TABLESPACE tablespace|PCTFREE integer|PCTUSED integer|INITRANS integer|MAXTRANS integer|deferred_segment_creation|object_affinity_clause).
```

**[list\_partitions](#listpartitions)::=**

```ebnf
= PARTITION BY LIST "(" column {"," column} ")" "(" (PARTITION [partname] 
list_values_clause  [table_partition_description | lob_clauses])
{"," (PARTITION [partname]  list_values_clause  [table_partition_description | lob_clauses])} ")".
```

**[list\_values\_clause](#listvaluesclause)::=**

```ebnf
= VALUES "(" (DEFAULT|list_values) ")".
```

**[list\_values](#listvaluesclause)::=**

```ebnf
= ((literal|NULL) {"," (literal|NULL)})
|("(" ((literal|NULL) {"," (literal|NULL)})")" {"," "(" ((literal|NULL) {"," (literal|NULL)})")"}).
```

**[hash\_partitions](#hashpartitions)::=**

~~~ebnf
= PARTITION BY HASH "(" (column {"," column}) ")"
(individual_partition_clause | hash_partitions_by_quantity).
~~~

**[individual\_partition\_clause](#individualpartitionclause)::=**

~~~ebnf
= "(" ( PARTITION [partname]  [partition_storage_clause | lob_clauses] )
{"," ( PARTITION [partname]  [partition_storage_clause | lob_clauses] )}")".
~~~

**[partition\_storage\_clause](#partitionstorageclause)::=**

```ebnf
= TABLESPACE tablespace. 
```

**[hash\_partitions\_by\_quantity](#hashpartitionsbyquantity)::=**

```ebnf
= PARTITIONS hash_partition_quantity [STORE IN "(" (tablespace {"," tablespace}) ")"] 
[OVERFLOW STORE IN "(" (tablespace {"," tablespace}) ")"].
```

**[composite_range_partitions](#compositerangepartitions)::=**

```ebnf
= PARTITION BY RANGE "(" (column {"," column}) ")"
 [interval_clause]  (subpartition_by_range|subpartition_by_list|subpartition_by_hash)
"(" (range_partition_desc {"," range_partition_desc}) ")".
```

**[subpartition_by_range](#subpartitionbyrange)::=**

```ebnf
= SUBPARTITION BY RANGE "(" (column {"," column}) ")" [subpartition_template].
```

**[subpartition_by_list](#subpartitionbylist)::=**

```ebnf
= SUBPARTITION BY LIST "(" (column {"," column}) ")" [subpartition_template].
```

**[subpartition_by_hash](#subpartitionbyhash)::=**

```ebnf
= SUBPARTITION BY HASH "(" (column {"," column}) ")" 
[(subpartition_template | hash_subparts_by_quantity)].
```

**subpartition_template::=**

```ebnf
= SUBPARTITION TEMPLATE (("(" (
(range_subpartition_desc {"," range_subpartition_desc})
| (list_subpartition_desc {"," list_subpartition_desc})
| (individual_hash_subparts { "," individual_hash_subparts})) ")")).
```

**[range_subpartition_desc](#rangesubpartitiondesc)::=**

```ebnf
= (SUBPARTITION [subpartname] range_values_clause [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation]).
```

**[list\_subpartition_desc](#listsubpartitiondesc)::=**

```ebnf
= (SUBPARTITION [subpartname] list_values_clause [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation]).
```

**[individual_hash_subparts](#individualhashsubparts)::=**

```ebnf
= SUBPARTITION [subpartname] [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation].
```

**[hash_subparts_by_quantity](#hashsubpartsbyquantity)::=**

```ebnf
= SUBPARTITIONS integer [STORE IN "(" (tablespace {"," tablespace}) ")"].
```

**[range_partition_desc](#rangepartitions)::=**

```ebnf
= PARTITION [partname]  range_values_clause  [table_partition_description]
[hash_subparts_by_quantity | ("(" (
(range_subpartition_desc {"," range_subpartition_desc})
| (list_subpartition_desc {"," list_subpartition_desc})
| (individual_hash_subparts {"," individual_hash_subparts})) ")")].
```

**[composite_list_partitions](#compositelistpartitions)::=**

```ebnf
= PARTITION BY LIST "(" (column {"," column}) ")"
(subpartition_by_range | subpartition_by_list | subpartition_by_hash)
"(" (list_partition_desc {"," list_partition_desc}) ")".
```

**[list_partition_desc](#listpartitions)::=**

```ebnf
= PARTITION [partname]  list_values_clause [table_partition_description]
[hash_subparts_by_quantity | ("(" (
(range_subpartition_desc {"," range_subpartition_desc})
| (list_subpartition_desc {"," list_subpartition_desc})
| (individual_hash_subparts {"," individual_hash_subparts})) ")")].
```

**[composite\_hash\_partitions](#compositehashpartitions)::=**

```ebnf
= PARTITION BY HASH "(" (column {"," column}) ")"
(subpartition_by_range | subpartition_by_list | subpartition_by_hash)
(("(" (hash_partition_desc {"," hash_partition_desc}) ")") | hash_partitions_by_quantity).
```

**[hash\_partition\_desc](#hashpartitiondesc)::=**

```ebnf
= PARTITION [partname] [table_partition_description]
["(" (
(range_subpartition_desc {"," range_subpartition_desc})
| (list_subpartition_desc {"," list_subpartition_desc})
| (individual_hash_subparts {"," individual_hash_subparts})) ")"].
```

**[consistent_hash_partitions](#consistenthashpartitions)::=**

~~~ebnf
= PARTITION BY [CONSISTENT] HASH "(" (column {"," column}) ")"
(individual_partition_clause | hash_partitions_by_quantity).
~~~

**[consistent_hash_with_subpartitions](#consistenthashwithsubpartitions)::=**

~~~ebnf
= PARTITION BY [CONSISTENT] HASH "(" (column {"," column}) ")"
(subpartition_by_range|subpartition_by_list|subpartition_by_hash)
(("(" (hash_partition_desc {"," hash_partition_desc}) ")") | hash_partitions_by_quantity).
~~~

**[lob\_clauses](#lobclause)::=**

```ebnf
= lob_clause {" " lob_clause}.
```

**[lob\_clause](#lobclause)::=**

```ebnf
= LOB "("column {"," column} ")" STORE AS [BASICFILE|SECUREFILE]
"("
    (
    TABLESPACE space_name
    |(ENABLE|DISABLE) STORAGE IN ROW
    | CHUNK integer
    | (CACHE | NOCACHE | CACHE READS) [LOGGING | NOLOGGING]
    | ((COMPRESS [LOW|MEDIUM|HIGH])|NOCOMPRESS)
    | (DEDUPLICATE|KEEP_DUPLICATES)
    )
{
    " "
    (
    TABLESPACE space_name
    |(ENABLE|DISABLE) STORAGE IN ROW
    | CHUNK integer
    | (CACHE | NOCACHE | CACHE READS) [LOGGING | NOLOGGING]
    | ((COMPRESS [LOW|MEDIUM|HIGH])|NOCOMPRESS)
    | (DEDUPLICATE|KEEP_DUPLICATES)
    )
}
")".
```

**[logging\_clause](#loggingclause)::=**

```ebnf
= LOGGING | NOLOGGING.
```

**[parallel\_clause](#parallelclause)::=**

```ebnf
= NOPARALLEL | PARALLEL [integer].
```

**[cache\_clause](#cacheclause)::=**

```ebnf
= CACHE | NOCACHE.
```

**[readonly\_clause](#readonlyclause)::=**

```ebnf
= READONLY | READWRITE.
```

**[inmemory\_clause](#inmemoryclause)::=**

```ebnf
= INMEMORY | NO INMEMORY.
```

**[table\_compression](#tablecompression)::=**

```ebnf
= COMPRESS | NOCOMPRESS.
```

**[nested_table_clauses](#nestedtableclause)::=**

```ebnf
= nested_table_clause {" " nested_table_clause}.
```

**[nested_table_clause](#nestedtableclause)::=**

```ebnf
= NESTED TABLE (nested_item|COLUMN_VALUE) [LOCAL|GLOBAL] STORE AS 
storage_table ["(" nested_table_clause ")"] ["(" TABLESPACE tablespace_name ")"].
```

**[table_encryption_clause](#tableencryptionclause)::=**

```ebnf
= ENCRYPT [USING ("'" AES128 "'" | "'" SM4 "'")].
```

**[object_affinity_clause](#objectaffinityclause)::=**

```ebnf
= OBJECT AFFINITY (default|INSTANCE instance_number|auto).
```

**[lsc_table_properties](#lsctableproperties)::=**

```ebnf
= [compression_clause] [table_sort_clause] [mcol_ttl_clause] [transformer_clause] [mcolability_clause] storage_table.
```

**[table_sort_clause](#tablesortclause)::=**

```ebnf
= ORDER BY "("column_name{"," column_name}")" [NULLS (FIRST|LAST)] [ASC|DESC] [SCOL].
```

**[mcol_ttl_clause](#mcolttlclause)::=**

```ebnf
= MCOL TTL timestamp.
```

**[transformer_clause](#transformerclause)::=**

```ebnf
= (ENABLE|DISABLE) ((TRANSFORM|COMPACT|BUILD AC) {(TRANSFORM|COMPACT|BUILD AC)}).
```

**[mcolability_clause](#mcolabilityclause)::=**

```ebnf
= (ENABLE|DISABLE) (MCOL).
```

**[row\_movement\_clause](#rowmovementclause)::=**

```ebnf
= (ENABLE|DISABLE) ROW MOVEMENT.
```

<span id="temptable" name="temptable"></span>

### GLOBAL TEMPORARY

该语句只作用于HEAP表和单机TAC表，用于指定创建的表为全局临时表。全局临时表对数据库所有会话（Session）可见，但表中数据在各会话间隔离，即每个会话只能看到在本会话中插入的数据。

全局临时表被创建后将一直存在（除非被DROP），ON COMMIT DELETE | PRESERVE ROWS则定义了其数据在事务提交后是否保留，具体在[temp\_table\_attr\_clause](#temptableattrclause)中说明。

示例（HEAP表、单机TAC表）

```sql
CREATE GLOBAL TEMPORARY TABLE globaltemtable(c1 int,c2 int);
```

<span id="privatetemptable" name="privatetemptable"></span>

### PRIVATE TEMPORARY

该语句只作用于HEAP表和单机TAC表，用于指定创建的表为私有临时表。在某个会话中创建的私有临时表，其表结构及数据只对本会话可见。

私有临时表的数据在当前会话中一直保留，且不会在事务提交时被删除，ON COMMIT DROP | PRESERVE DEFINITION则定义了事务提交后是否DROP该表，具体在[temp\_table\_attr\_clause](#temptableattrclause)中说明。

私有临时表的名称必须以`YAS$PTT_`或`ORA$PTT_`开头，其他表的名称则不允许以`YAS$PTT_`或`ORA$PTT_`开头。

私有临时表不允许创建约束。

示例（HEAP表、单机TAC表）

```sql
CREATE PRIVATE TEMPORARY TABLE YAS$PTT_privatetemtable(c1 int,c2 int);

-- 如不以YAS$PTT_或ORA$PTT_开头会返回错误
CREATE PRIVATE TEMPORARY TABLE privatetemtable(c1 int,c2 int);
YAS-02170 create a private temporary table with a name not matching 'YAS$PTT_' prefix
```

<span id="shardtable" name="shardtable"></span>

### SHARDED

该语句只在存算一体分布式集群部署中使用，表示创建一张分布表，分布表在每个数据节点上依据Chunk将数据分片存储。

YashanDB将分布表的每一个分区（Partition）作为一个Chunk，插入表中的数据将按照指定的规则被分配到各个Chunk中，可通过[consistent\_hash\_partitions](#consistenthashpartitions)语句定义这种规则，不指定规则时，将对表按如下规则进行哈希分区来分片存储数据：

- 如果表上定义了主键列，则分区键列为主键列。
- 如果表上未定义主键列，但存在唯一索引，则分区键为所有唯一索引的公共子集中首个恰当数据类型的字段。
- 否则，分区键为表的首个恰当数据类型的列字段。

SHARDED为YashanDB存算一体分布式数据库的默认建表方式，即CREATE TABLE=CREATE SHARDED TABLE。

示例（存算一体分布式集群部署）

```sql
-- 创建默认的分布表并插入记录
CREATE SHARDED TABLE area_shard
(area_no CHAR(2) NOT NULL,
area_name VARCHAR2(60),
DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

-- 查询表属性，sharded=Y表示为分布表
select table_name,table_type,partitioned,sharded,duplicated 
from user_tables 
where table_name='AREA_SHARD';
TABLE_NAME                 TABLE_TYPE PARTITIONED SHARDED DUPLICATED
-------------------------- ---------- ----------- ------- ----------
AREA_SHARD                 LSC        N           Y       N

-- 查询表分区数量，即Chunk数量
select count(1) 
from user_tab_partitions 
where table_name='AREA_SHARD';
             COUNT(1)
---------------------
                    6
```

<span id="duplicatetable" name="duplicatetable"></span>

### DUPLICATED

该语句只在存算一体分布式集群部署中使用，表示创建一张复制表，插入表中的数据将被完全复制到各个数据节点中。

示例（存算一体分布式集群部署）

```sql
-- 创建复制表
CREATE DUPLICATED TABLE area_dupli
(area_no CHAR(2) NOT NULL,
area_name VARCHAR2(60),
DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
 
-- 查询表属性，duplicated=Y表示为复制表
select table_name,table_type,partitioned,sharded,duplicated 
from user_tables 
where table_name='AREA_DUPLI';
TABLE_NAME                 TABLE_TYPE PARTITIONED SHARDED DUPLICATED
-------------------------- ---------- ----------- ------- ----------
AREA_DUPLI                 LSC        N           N       Y
```

### IF NOT EXISTS

该语句用于在CREATE表之前，先判断该表是否存在，省略则不会判断。若不省略，此时如果要创建的表已存在，系统将不会报错，并且使用旧表，不会创建新的表。

示例

```sql
-- 创建已存在的表area时，不会报错，且新表未创建成功
CREATE TABLE IF NOT EXISTS area(c1 INT, c2 INT);

SELECT * FROM area WHERE area_no='01';
AREA_NO AREA_NAME        DHQ       
------- ---------------- ----------
01      EastChina           Shanghai    
```

### table\_name

该语句用于指定创建的表的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

如表名称里出现了`.`符号，表示该符号之前为用户名、之后为表名，此时必须拥有在指定用户名下创建表对象的权限。

<span id="objecttable" name="objecttable"></span>

### object\_table

通过OF子句显式创建一个object_type对应的对象表。对象表的列和object_type的属性一一对应。

> **Caution**: 
>
> 对象表功能属于实验室特性，**不推荐在生产环境中使用**，以免影响系统稳定性。

<span id="relationproperties" name="relationproperties"></span>

### relation\_properties

该语句用于指定创建的表的结构，包括列字段（Column）和约束（Constraint）。

<span id="columndefinition" name="columndefinition"></span>

#### column\_definition

该语句用于定义表的列字段项。

##### column_name

列字段的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

##### dataType

指定列字段的数据类型。查看YashanDB的[数据类型描述](../数据类型/00数据类型)。

其中，数据类型指定为LOB/JSON类型时，可通过[lob_clauses](#lobclause)子句指定其存储属性。

数据类型指定为Nested Table UDT类型时，必须通过[nested_table_clauses](#nestedtableclause)子句创建嵌套表，且不能为[临时表](#temptable)的列字段指定Nested Table UDT类型。

##### VISIBLE|INVISIBLE

该关键字用于指定列字段是否可见，默认为可见（VISIBLE）。

不可见列（又称“隐藏列”）的使用约束如下：

- 仅适用于HEAP表，且不能为临时表、外部表。

- 不可见列的数据类型不能为UDT。

- 同一个表中不能全为不可见列，必须至少有1个可见列。

- 操作不可见列时必须显式指定列名，包括SELECT、INSERT、导入/导出等，例如SELECT * FROM语句无法查询不可见列。

示例 （HEAP表）

```sql
-- 创建包含不可见列的表
CREATE TABLE product
(product_no CHAR(5) PRIMARY KEY,
 product_name VARCHAR2(30),
 cost NUMBER INVISIBLE,
 price NUMBER
);
-- 直接插入数据，会报错
INSERT INTO product VALUES ('11001','product001',8,10);

YAS-04304 the count of column is mismatched

-- 插入数据需显式指定不可见列的列名
INSERT INTO product VALUES ('11001','product001',10);
INSERT INTO product (product_no,product_name,cost,price) VALUES ('11002','product002',13,16);

-- SELECT * FROM语句无法查询不可见的cost列
SELECT * FROM product;

PRODUCT_NO PRODUCT_NAME                            PRICE
---------- --------------------------------- -----------
11001      product001                                 10
11002      product002                                 16

-- 显式指定不可见列名方可成功查询
SELECT product_name,cost FROM product;

PRODUCT_NAME                             COST
--------------------------------- -----------
product001
product002                                 13
```

<span id="identityclause" name="identityclause"></span>

##### identity_clause

该语句用于为指定列的身份属性， 一个表只能有一个身份列，身份列必须为数值类型。

* GENERATED ALWAYS AS IDENTITY表示在插入数据时，身份列的值总是由序列生成。  
* GENERATED BY DEFAULT AS IDENTITY表示在插入数据时，如果未指定该列的值，则由序列生成。  
* GENERATED BY DEFAULT ON NULL AS IDENTITY表示在插入数据时，如果未指定该列的值或者指定该列的值为NULL，则由序列生成。  

<span id="identityoptions" name="identityoptions"></span>

###### identity_options

该语句用于为指定身份列的sequence属性。  

* START WITH用于指定序列号生成器的起始值，该值需介于MINVALUE和MAXVALUE之间。省略则默认为1（升序序列号生成器）或-1（降序序列号生成器）。  
* INCREMENT BY用于指定序列号生成器的增量（Interval），即下一序列号值=当前序列号值+增量。该值不能为0，省略则默认为1。  
* MAXVALUE/NOMAXVALUE用于指定序列号生成器的最大值，对于升序序列号生成器，NOMAXVALUE表示为操作系统的最大正整数数值；对于降序序列号生成器，NOMAXVALUE表示为-1。  
* INVALUE/NOMINVALUE用于指定序列号生成器的最小值，对于升序序列号生成器，NOMINVALUE表示为1；对于降序序列号生成器，NOMAXVALUE表示为操作系统的最大负整数数值。  
* CYCLE/NOCYCLE用于指定当生成的序列号到达MAXVALUE（升序序列号生成器）或MINVALUE（降序序列号生成器）后，是否开启序列号循环。  
* ORDER/NOORDER用于指定是否保证序列号按请求的先后顺序生成。  
* CACHE/NOCACHE用于指定是否在内存中对序列号进行预分配，省略则默认为在内存中预分配20个序列号。  
* NOKEEP/NOSCALE仅语法支持，无实际含义。  

示例 （HEAP表）

```sql
-- generated always
CREATE TABLE area(
    area_id number generated always as identity,
    area_name VARCHAR2(60),
    DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL
);

-- generated by default on null
CREATE TABLE area(
    area_id number generated by default on null as identity(start with 100 increment by 1),
    area_name VARCHAR2(60),
    DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL
);
```

##### DEFAULT default\_expr

列字段的缺省值。default\_expr可以为字面量、运算式、函数等表达式。

XMLTYPE类型的列不允许配置缺省值。

<span id="codecexpr" name="codecexpr"></span>

##### codec_expr

对于LSC表的列字段，使用本语句定义列字段的编码压缩属性，系统按此设置存储该列上的数据。

对于单机TAC表的列字段，使用本语句定义列字段的字典编码属性，系统按此设置存储该列上的数据。

<span id="compressionclause" name="compressionclause"></span>

###### compression_clause

该语句用于对于LSC表定义压缩属性。

- 压缩算法：可指定为LZ4或ZSTD压缩算法，或指定为UNCOMPRESSED不对数据进行压缩。
- 压缩级别：指定压缩算法为LZ4或ZSTD后，可指定按LOW、MEDIUM、HIGH三个级别进行压缩。若不指定级别，会根据配置项COMPRESSION_LEVEL默认指定。

对于某一个列上的数据，基于表列级别定义的压缩属性与系统默认的压缩属性之间的优先关系如下：

列级压缩属性 ＞ 表级压缩属性 ＞ 系统参数COMPRESSION与COMPRESSION_LEVEL的相关配置。

建议您根据数据库的使用场景，选择合适的压缩算法。LZ4压缩算法的优点是：解压与压缩速度快，并且有着较好的压缩效果，ZSTD压缩算法相对于LZ4压缩算法，解压和压缩速度较慢，但是压缩效果更好。

- 如果您不能确定表的使用场景，推荐您使用系统默认的LZ4 LOW进行压缩，大多数场景下，LZ4可以用较少的CPU资源，减少IO需要时间。
- 对于有高查询性能要求的场景，建议您使用LZ4进行压缩，压缩等级需要根据您对数据导入和冷热数据转换的性能要求来决定，低压缩等级时数据导入和冷热转换速度更快。
- 对于归档数据，且查询性能要求较为宽松，建议您使用ZSTD进行压缩，等级可根据您对导入速度的要求进行调整。

YashanDB支持按表级别或列级别定义压缩属性，并提供如下配置参数用于设置系统默认的压缩属性。

<span id="compressiontype" name="compressiontype"></span>

**compression_type**

该子句用于指定压缩算法及压缩机别。

- COMPRESSION：是否进行数据压缩。
- COMPRESSION_LEVEL：数据压缩的级别。

<span id="encodingclause" name="encodingclause"></span>

###### encoding_clause

对于LSC表的列定义编码属性，指定按如下一种方式进行编码：

- PLAIN编码：即不编码，按原始方式存储数据。
- RLE编码：游程长度编码。
- DICTIONARY(PLAIN)：字典编码后的数据进行PLAIN编码。
- DICTIONARY(RLE)：字典编码后的数据进行RLE编码。
- BYTE-PACKED：根据数据字节长度编码。

不指定编码时，默认采用自适应编码，即内部自动根据数据类型与数据特征，自适应地探测合适的编码类型，对同一列在不同数据块中支持使用不同的算法来进行编码。

对于某一个列上的数据，是否应该编码和采用何种编码方式，与该列的数据类型和数据特点相关，如下为YashanDB对不同数据类型可适配的编码方式（✓表示适配，X表示不适配）：

|  数据类型| PLAIN编码| RLE编码| 字典编码| BYTE-PACKED编码|
| -------------------------------- | --------- | ------- | -------- | --------    |
| TINYINT/SMALLINT/大对象型         | ✓         | X       | X        | X           |
| INT/BIGINT/FLOAT/DOUBLE/日期时间型   | ✓         | ✓       | ✓        |X            |
| 字符型                           | ✓         | X       | ✓        |X            |
| NUMBER型                         |✓          |X        | X         |✓            |

其中，BOOLEAN类型数据由系统自动进行布尔编码存储，无需为其指定编码方式。长度大的CHAR字段采用字典编码对内存占用比较大，对于超过128字节的CHAR类型，请尽量不要选择字典编码。

示例（LSC表）

```sql
-- 创建LSC表，压缩指定列且对其字典编码存储
DROP TABLE IF EXISTS finance_info;
CREATE TABLE finance_info
(year CHAR(4) NOT NULL COMPRESSION lz4 HIGH ENCODING DICTIONARY(RLE),
month CHAR(2) NOT NULL,
branch CHAR(4),
revenue_total NUMBER(10,2),
cost_total NUMBER(10,2),
fee_total NUMBER(10,2)
);
```

对于TAC表的列定义字典编码属性，指定按DICTIONARY(PLAIN)方式进行编码，该属性仅支持数据类型为字符型的列使用。

同时TAC表的列使用该属性支持指定字典值上限，规格为[1,65535]，缺省默认字典值上限为65535。

示例（单机TAC表）

```sql
-- 创建TAC表，对指定列字典编码存储且指定字典上限为100
DROP TABLE IF EXISTS finance_info;
CREATE TABLE finance_info
(year CHAR(4) NOT NULL ENCODING DICTIONARY(PLAIN, 100),
month CHAR(2) NOT NULL,
branch CHAR(4),
revenue_total NUMBER(10,2),
cost_total NUMBER(10,2),
fee_total NUMBER(10,2)
);
```

<span id="columnencryptionclause" name="columnencryptionclause"></span>

###### column_encryption_clause

该语句用于为指定列定义加密属性。

列加密使用规则如下：

- 在单机/共享集群/分布式集群部署中，创建含加密列的表之前必须先完成密钥管理相关配置，包括创建钱包、开启钱包、设置主密钥等，具体操作请查阅[配置钱包](../../../产品安全/加密支持/存储加密/密钥管理.md#configuringwallet)。
- sys用户不可进行列加密。
- 仅适用于HEAP表和LSC表，且不能为[临时表](#temptable)。
- 需加密的目标列必须满足如下要求：
    - 若同时需为该列创建索引，只能指定为等值查询的BTREE索引。
    - 该列不能为分区键或子分区键。
    - 该列不能为[AC对象](./CREATE ACCESS CONSTRAINT)所依赖的列。
    - 该列不能为外键列或外键依赖的键。
    - 若为LSC表的列加密，该列的数据类型不能为LOB类型。
- 同一个表中的不同列，加密算法必须统一。
- 同时使用列加密和[表级加密](#tableencryptionclause)时，两者指定的算法必须统一。
- 列加密属性一经指定无法修改，且加密列的其他属性也无法修改。
- 对于LSC表的稳态数据列，如果同时指定了列加密和表空间加密，目标列将采用列加密算法。

**ENCRYPT**

对指定列开启列加密功能。

**USING encryption_algorithm**

该语句用于指定加密算法，支持AES128和SM4，可省略，省略时默认采用AES128算法。

> **Note**:
>
> 如需使用国密算法SM4、数据透明加密相关的密钥管理功能，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备.md)检查并确保服务器系统中已安装符合要求的工具。

示例（HEAP表、LSC表）

```sql
-- 创建员工信息表并对employee_ID列加密
DROP TABLE IF EXISTS encrypt_col_employees;
CREATE TABLE encrypt_col_employees
(branch CHAR(4), 
department CHAR(3), 
employee_no CHAR(10) NOT NULL PRIMARY KEY, 
employee_name VARCHAR2(10), 
employee_ID CHAR(18) ENCRYPT USING 'AES128', 
sex CHAR(1), 
entry_date DATE
);
```

<span id="inlineconstraint" name="inlineconstraint"></span>

##### inline\_constraint

该语句用于定义表的行内约束项。关于约束项的详细描述请参考通用SQL语法[constraint](../通用SQL语法/constraint)。

其中，不能为[临时表](#temptable)建立FOREIGN KEY约束，也不能为其他表建立到临时表的FOREIGN KEY约束。

对于除LSC表以外定义的约束项，默认启动并行检查约束。

设置并行度时，YashanDB会自动进行探测，当探测到当前表数据量为大于1G，小于当前DATA_BUFFER_SIZE参数值时，会按一半服务器CPU核数的并行度并发检查约束。

#### out\_of\_line\_constraint

该语句用于定义表的行外约束项。关于约束项的详细描述请参考通用SQL语法[constraint](../通用SQL语法/constraint)。

其中，不能为[临时表](#temptable)建立FOREIGN KEY约束，也不能为其他表建立到临时表的FOREIGN KEY约束。

<span id="tableproperties" name="tableproperties"></span>

### table\_properties

该语句用于指定创建的表的各项属性，多项间以空格进行分隔。

<span id="organizationclause" name="organizationclause"></span>

#### organization_clause

该语句指定表的数据存储方式（表类型），可省略，则系统按DEFAULT_TABLE_TYPE参数的值创建相应类型的表。YashanDB针对不同表类型的数据存储方式参考产品描述[存储引擎](../../../概念手册/YashanDB体系架构/存储引擎)。

##### HEAP

创建HEAP行存表。

##### TAC

创建TAC列存表。

##### LSC

创建LSC列存表，此种表类型要求默认或指定表空间为databucket表空间，否则创建失败。

##### EXTERNAL

创建外部表，外部表为一种表结构存储在数据库内，而数据存储在数据库外的特殊表类型。由于外部表的数据未存储在数据库内，因此基于行的所有DDL操作，例如索引、行迁移、闪回、加密等，均不适用于外部表。

外部表对应数据只能被查询，不可增删改。

对于外部表而言，关于segment的属性设置没有意义也不会实际生效。

###### external_table_clause

external_table_clause外部表所对应的外部数据结构定义，具体描述参考通用语法[EXTERNAL_TABLE](../通用SQL语法/external_table)。

本语句可省略，若省略则所创建的外部表不指定相应的外部数据结构，该表将无法正常查询。

<span id="temptableattrclause" name="temptableattrclause"></span>

#### temp\_table\_attr\_clause

该语句只针对[全局临时表](#temptable)和[私有临时表](#privatetemptable)，且DELETE|PRESERVE ROWS只可用于全局临时表，而DROP|PRESERVE DEFINITION只可用于私有临时表。

若省略该语句，默认值分别为DELETE ROWS和DROP DEFINITION。

> **Note**:  
> 当临时表设置了DELETE ROWS属性时，如果先对表进行了插入操作，然后开启自治事务，而且自治事务内进行了提交操作，那么自治事务内插入的数据将不会清理。

##### ON COMMIT DELETE|PRESERVE ROWS

指定事务提交后在当前会话中插入的全局临时表的数据是否保留，DELETE为删除，PRESERVE为保留。

示例（HEAP表、单机TAC表）

```sql
-- 创建全局临时表T_orders_info，并指定PRESERVE ROWS
CREATE GLOBAL TEMPORARY TABLE T_orders_info (
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10))
ON COMMIT PRESERVE ROWS;
  
-- 插入数据并提交
INSERT INTO T_orders_info VALUES ('20010102020001','product 001','02','0202',sysdate-400,'0001');
COMMIT;
  
-- 查询结果，由于指定了PRESERVE ROWS，则仍可以查询到数据（且只为本会话插入的数据），如为DELETE ROWS则无法查询到数据
SELECT order_no,order_desc,area,branch,order_date,salesperson FROM T_orders_info;
ORDER_NO          ORDER_DESC     AREA  BRANCH ORDER_DATE             SALESPERSON 
----------------- -------------- ----- ------ ---------------------- -------------
20010102020001    product 001    02    0202   2020-12-06 22:55:32    0001
```

##### ON COMMIT DROP|PRESERVE DEFINITION

指定当前会话中创建的私有临时表是否在事务提交后被删除，DROP表示删除，PRESERVE表示保留。

示例（单机HEAP表、TAC表）

```sql
-- 创建私有临时表YAS$PTT_orders_info，并指定DROP DEFINITION
CREATE PRIVATE TEMPORARY TABLE YAS$PTT_orders_info (
order_no CHAR(14),
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE,
salesperson CHAR(10))
ON COMMIT DROP DEFINITION;
  
-- 插入数据并提交
INSERT INTO YAS$PTT_orders_info VALUES ('20010102020001','product 001','02','0202',sysdate-400,'0001');
COMMIT;
  
-- 查询结果，由于指定了DROP DEFINITION，事务提交后PTT_orders_info表被删除
SELECT * FROM YAS$PTT_orders_info;
[1:15]YAS-02012 table or view does not exist
```

<span id="physicalattributeclause" name="physicalattributeclause"></span>

#### physical\_attribute\_clause

该语句用于指定创建的表的物理存储属性或创建表时是否立即创建segment。

##### TABLESPACE tablespace\_name

该语句用于指定表所在的表空间。

对于[临时表](#temptable)，只能指定一个temporary类型的表空间，省略则默认为表所属用户所在的表空间（临时表的表空间默认为数据库创建时生成的temporary表空间）。

对于LSC表，为其所指定的表空间必须拥有bucket属性，详见[CREATE TABLESPACE](./CREATE TABLESPACE)描述。其中，系统的缺省表空间（DEFAULT表空间）已默认拥有bucket属性，可以作为LSC表的表空间。

存算一体分布式集群部署中，创建分布表时不能使用本语句指定表空间。

##### TABLESPACE SET tablespace_set\_name

指定分布表所在的表空间集。省略时，或者指定为 `DEFAULT` 时，默认为表用户的默认表空间集。若用户未指定默认表空间集，使用内置的USERS表空间集。

##### PCTFREE/PCTUSED/INITRANS/MAXTRANS

指定表的PCTFREE/PCTUSED/INITRANS/MAXTRANS属性，省略则默认为8/NULL/2/255。

其中：

- PCTFREE：表示数据块为数据库对象进行UPDATE保留的空间百分比，当可用空间低于该百分比时无法进行INSERT，只能进行UPDATE。
- PCTUSED：表示数据块为数据库对象保留的最小已用空间百分比，当数据所占空间低于该百分比时可进行INSERT。
- INITRANS：表示每个数据块中初始并发事务项的数量。
- MAXTRANS：表示每个数据块中并发事务项数量的最大值。

##### storage\_clause

指定为表分配的初始空间和最大空间大小，由于YashanDB采取延迟分配的策略（有数据才分配空间），不建议指定此参数。

<span id="deferredsegmentcreation" name="deferredsegmentcreation"></span>

##### deferred\_segment\_creation

<span id="segmentcreation" name="segmentcreation"></span>

###### SEGMENT CREATION DEFERRED|IMMEDIATE

用于指定创建表对象时segment的创建方式，该语法不适用于临时表。

- IMMEDIATE：表示立即创建。
- DEFERRED：表示延迟创建，默认为此方式。

示例（单机/共享集群/分布式集群部署）

```sql
CREATE TABLE employee_info (
name CHAR(10) NOT NULL,
age INT,
id INT NOT NULL
)tablespace users PCTFREE 50 PCTUSED 20 INITRANS 3 MAXTRANS 254 SEGMENT CREATION DEFERRED;
```

<span id="tablepartitionclause" name="tablepartitionclause"></span>

#### table\_partition\_clause

该语句用于为表创建分区（Partition）或组合分区，可通过USER_TAB_PARTITIONS和USER_TAB_SUBPARTITIONS视图查看所有分区和子分区信息。

该语句包含如下规则：

- 不允许为[临时表](#temptable)创建分区。

- 不允许将LOB列/JSON列指定为分区列和子分区列。

- 不允许为[嵌套表](#nestedtableclause)创建子分区。

- 在存算一体分布式集群部署中，根据表的分布类型不同创建分区/组合分区时的语法不同：

    - 对于[复制表](#duplicatetable)：需使用{%}_partitions系列语句创建相应的分区；需使用composite_{%}_partitions系列语句创建相应的组合分区。

    - 对于[分布表](#shardtable)：需使用[consistent\_hash\_partitions](#consistenthashpartitions)语句创建分区，其分区数等同于Chunk数；需使用[consistent_hash_with_subpartitions](#consistenthashwithsubpartitions)创建组合分区，其子分区名为`分区名_子分区名`。

<span id="rangepartitions" name="rangepartitions"></span>

##### range\_partitions

创建范围分区，分区列为多项时以`,`分隔。

如果指定了INTERVAL，那么分区列的数量必须为1，并且只能是数值或者时间类型。

<span id="intervalclause" name="intervalclause"></span>

###### interval\_clause

创建INTERVAL类型的范围分区。

**INTERVAL(expr)**

范围分区根据分区界值将每个分区定义了下限值（即为前一分区的上限值）和上限值（分区界值），最后的一个分区其上限值即为最大的分区界值。指定INTERVAL表示，当要插入的数据超过最大分区界值时，系统将根据如下规则创建一个新分区，并将这行数据插入到新分区中：

*   新分区由系统自动命名。
*   新分区的上限值=最大分区界值+INTERVAL值，之后新分区的上限值将成为最大分区界值。
*   如果最大分区界值已被定义为MAXVALUE，则无法创建新分区，该数据无法插入表。
*   expr为表达式，表达式的结果类型需与分区列字段的数据类型一致。

此时，如表上已建立了分区索引（Local Partitioned Index），则系统在创建新分区的同时，也会创建相应的索引分区。

**STORE IN**

为INTERVAL分区指定表空间，指定多个表空间以`,`分隔，则系统在创建新分区时，按照此处定义的顺序，循环使用作为新分区的表空间。未指定本语句时分区的表空间默认为表所在的表空间。

示例（单机/共享集群/分布式集群部署）

```sql
-- 创建范围分区表orders_info，并指定INTERVAL值
DROP TABLE IF EXISTS orders_info;
CREATE TABLE orders_info (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
TABLESPACE yashan
PARTITION BY RANGE (id)
INTERVAL (1000)
(PARTITION p_orders_info_1 VALUES LESS THAN (800));
  
-- 插入三条数据均成功，如未指定INTERVAL值，则只有第一条数据可以插入成功
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',sysdate-400,'0001',300);
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',sysdate-400,'0001',2000);
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',sysdate-400,'0001',20000);
COMMIT;
  
-- 查询该表上的分区，除p_orders_info_1 分区外，系统自动创建了另外两个分区
SELECT TABLE_NAME t_name,TABLESPACE_NAME ts_name,PARTITION_NAME par_name,HIGH_VALUE par_max FROM USER_TAB_PARTITIONS WHERE TABLE_NAME='ORDERS_INFO';
T_NAME             TS_NAME   PAR_NAME         PAR_MAX  
----------------- ---------- ---------------- ---------
ORDERS_INFO       YASHAN     SYS_P22          20800    
ORDERS_INFO       YASHAN     SYS_P21          2800     
ORDERS_INFO       YASHAN     P_ORDERS_INFO_1  800   

-- 创建分区键为时间类型的INTERVAL范围分区表，并指定STORE IN语句
DROP TABLE IF EXISTS orders_info;
CREATE TABLE orders_info (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
PARTITION BY RANGE (order_date)
INTERVAL (interval '3' month) STORE IN (yashan1,yashan2)
(PARTITION p_firstquarter VALUES LESS THAN (date '2000-01-01'));
```

<span id="rangevaluesclause" name="rangevaluesclause"></span>

###### range\_values\_clause

定义范围分区的上限值。

**literal**

分区列为多项时，则此处的上限值用`,`分隔并与分区列一一对应。

**MAXVALUE**

MAXVALUE表示无上限，即超过前一分区的上限的所有数据均进入此分区中，包括NULL。如所有分区列都定义了以MAXVALUE作为上限值的分区，不允许再继续定义MAXVALUE分区。

在定义了MAXVALUE的分区后，不能通过ADD PARTITION语句增加新的分区，仅能通过SPLIT PARTITION语句将已存在分区划分成新的分区。

示例（单机/共享集群/分布式集群部署）

```sql
DROP TABLE IF EXISTS orders_maxvalue;
CREATE TABLE orders_maxvalue(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
PARTITION BY RANGE(id)
(PARTITION p_orders_max_1 VALUES LESS THAN (800),
 PARTITION p_orders_max_2 VALUES LESS THAN (1000),
 PARTITION p_orders_max_3 VALUES LESS THAN (1500),
 PARTITION p_orders_max_4 VALUES LESS THAN (MAXVALUE)
);

-- 创建分区列为多项的范围分区表
DROP TABLE IF EXISTS orders_multikey;
CREATE TABLE orders_multikey(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
PARTITION BY RANGE(id,order_date)
(PARTITION p_orders_max_1 VALUES LESS THAN (800,'2010-01-01'),
 PARTITION p_orders_max_2 VALUES LESS THAN (1000,'2010-05-01'),
 PARTITION p_orders_max_3 VALUES LESS THAN (1500,'2010-10-01')
);

-- 所有分区列均定义MAXVALUE分区后继续定义MAXVALUE分区，此时返回错误
DROP TABLE IF EXISTS orders_multimax;
CREATE TABLE orders_multimax(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
PARTITION BY RANGE(id,order_date)
(PARTITION p_orders_max_1 VALUES LESS THAN (800,'2010-01-01'),
 PARTITION p_orders_max_2 VALUES LESS THAN (1000,'2010-05-01'),
 PARTITION p_orders_max_3 VALUES LESS THAN (1500,'2010-10-01'),
 PARTITION p_orders_max_4 VALUES LESS THAN (MAXVALUE,'2010-10-01'),
 PARTITION p_orders_max_5 VALUES LESS THAN (MAXVALUE,MAXVALUE),
 PARTITION p_orders_max_6 VALUES LESS THAN (MAXVALUE,MAXVALUE)
);

YAS-04240 partition bound is too high
```

<span id="tablepartitiondescription" name="tablepartitiondescription"></span>

###### table\_partition\_description


定义范围分区的存储属性。

各属性定义与[physical_attribute_clause](#physicalattributeclause)语句中的描述一致。若不指定，分区默认存储在表所在的表空间，PCTFREE/PCTUSED/INITRANS/MAXTRANS属性的默认为8/NULL/2/255。

在共享集群部署中，分区的资源亲和属性与[object_affinity_clause](#objectaffinityclause)语句中的描述一致。



示例（单机部署）

```sql
-- 创建定义了存储属性的范围分区
DROP TABLE IF EXISTS sales_info_range;
CREATE TABLE sales_info_range
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY RANGE(year)
(PARTITION p_sales_info_range_1 VALUES LESS THAN('2011') PCTFREE 3 SEGMENT CREATION DEFERRED,
 PARTITION p_sales_info_range_2 VALUES LESS THAN('2021'),
 PARTITION p_sales_info_range_3 VALUES LESS THAN('2031'));
```

<span id="listpartitions" name="listpartitions"></span>

##### list\_partitions

创建列表分区，分区列为多项时以`,`分隔。

<span id="listvaluesclause" name="listvaluesclause"></span>

###### list\_values\_clause

定义列表分区的列表内容。

**DEFAULT**

DEFAULT表示将在已定义列表值之外的所有数据均进入此分区中，如不指定DEFAULT分区，插入已定义列表值之外的所有数据会报错。如指定，须作为最后一个分区指定，且DEFAULT分区只允许指定一次。

在定义了DEFAULT的分区后，不能通过ADD PARTITION语句增加新的分区，仅能通过SPLIT PARTITION语句将已存在分区划分成新的分区。

**list_values**

分区列为多项时，须用`,`分隔并与分区列一一对应，该列表内容须指定为字面量或NULL。

###### table\_partition\_description


定义范围分区的存储属性。

各属性定义与[physical_attribute_clause](#physicalattributeclause)语句中的描述一致。若不指定，分区默认存储在表所在的表空间，PCTFREE/PCTUSED/INITRANS/MAXTRANS属性的默认为8/NULL/2/255。

在共享集群部署中，分区的资源亲和属性与[object_affinity_clause](#objectaffinityclause)语句中的描述一致。



示例（单机/共享集群/分布式集群部署）

```sql
-- 创建列表分区的销售信息表
DROP TABLE IF EXISTS sales_info;
CREATE TABLE sales_info
(year CHAR(4) NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4) NOT NULL,
product CHAR(10) NOT NULL,
quantity NUMBER DEFAULT 0 NOT NULL,
amount NUMBER(10,2) DEFAULT 0 NOT NULL,
salsperson CHAR(10))
PARTITION BY LIST(year,month)
(PARTITION p_sales_info1_1 VALUES (('2018','01'),('2018','02')),
PARTITION p_sales_info1_2 VALUES ('2020','01'),
PARTITION p_sales_info1_3 VALUES (DEFAULT));
```

<span id="hashpartitions" name="hashpartitions"></span>

##### hash\_partitions

创建哈希分区，分区列为多项时以`,`分隔，哈希分区有如下两种创建方式：

- 逐个指定哈希分区。
- 使用hash分数批量指定哈希分区。

<span id="individualpartitionclause" name="individualpartitionclause"></span>

###### individual\_partition\_clause

本语句用于逐个指定哈希分区，对每个分区分别定义其名称和存储属性，分区之间用`,`分隔。

<span id="partitionstorageclause" name="partitionstorageclause"></span>

**partition\_storage\_clause**

定义哈希分区的存储属性。

**TABLESPACE tablespace\_name**

为分区指定一个表空间，省略则默认为表所在的表空间。

此操作不适用于存算一体分布式集群部署。

示例（单机部署）

```sql
DROP Table if exists sales_info_hash;
CREATE TABLE sales_info_hash
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY hash(year)
(PARTITION p_sales_info_hash_1,
 PARTITION p_sales_info_hash_2);
 
-- 为分区指定表空间
DROP Table if exists sales_info_hashpar;
CREATE TABLE sales_info_hashpar
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4))
PARTITION BY hash(year)
(PARTITION p_sales_info_hash_1 TABLESPACE users,
 PARTITION p_sales_info_hash_2);
```

<span id="hashpartitionsbyquantity" name="hashpartitionsbyquantity"></span>

###### hash_partitions_by_quantity

本语句用于按哈希分数批量指定哈希分区，所有分区名称由系统生成。

**hash_partition_quantity**

用于指定哈希分区的数量。

**STORE IN**

用于指定哈希分区所属的表空间，表空间可指定多个，且数量无需与分区的数量一致，系统将按本语句中表空间指定的顺序进行循环归属。

不指定本语句时，系统默认使用表所在的表空间作为所有分区的表空间。

示例（单机部署）

```sql
-- 通过hash分数创建哈希分区表，并指定STORE IN语句
DROP TABLE IF EXISTS sales_info_hash;
CREATE TABLE sales_info_hash
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY hash(year)
PARTITIONS 4
STORE IN (yashan1,yashan2);
```

<span id="compositerangepartitions" name="compositerangepartitions"></span>

##### composite_range_partitions

该语句用于定义范围分区的组合分区，即一级分区采用范围分区，子分区（又称“二级分区”）按需指定为范围、列表或哈希分区之一。

一级分区的各属性定义与[range_partitions](#rangepartitions)语句中的描述一致。

<span id="subpartitionbyrange" name="subpartitionbyrange"></span>

###### subpartition_by_range

该语句用于定义范围子分区的分区列或子分区模板。

各属性定义与[range_partitions](#rangepartitions)语句中对应的描述一致。

<span id="rangesubpartitiondesc" name="rangesubpartitiondesc"></span>

**SUBPARTITION TEMPLATE (range_subpartition_desc)**



该语句可以创建范围子分区的模板，为分区批量创建相同定义的子分区，避免冗杂操作。当组合分区表的某个分区没有定义子分区时，使用子分区模板为默认子分区定义。

模板中必须为子分区定义名称，格式为`分区名_子分区名`，名称总长度不能大于64字节。


<span id="subpartitionbylist" name="subpartitionbylist"></span>

###### subpartition_by_list

该语句用于定义列表子分区的分区列或子分区模板。

各属性定义与[list_partitions](#listpartitions)语句中对应的描述一致。

<span id="listsubpartitiondesc" name="listsubpartitiondesc"></span>

**SUBPARTITION TEMPLATE (list_subpartition_desc)**



该语句可以创建范围子分区的模板，为分区批量创建相同定义的子分区，避免冗杂操作。当组合分区表的某个分区没有定义子分区时，使用子分区模板为默认子分区定义。

模板中必须为子分区定义名称，格式为`分区名_子分区名`，名称总长度不能大于64字节。


<span id="subpartitionbyhash" name="subpartitionbyhash"></span>

###### subpartition_by_hash

该语句用于定义哈希子分区的分区列或子分区模板。

各属性定义与[hash_partitions](#hashpartitions)语句中对应的描述一致。

<span id="individualhashsubparts" name="individualhashsubparts"></span>

**SUBPARTITION TEMPLATE (individual_hash_subparts)**



该语句可以创建范围子分区的模板，为分区批量创建相同定义的子分区，避免冗杂操作。当组合分区表的某个分区没有定义子分区时，使用子分区模板为默认子分区定义。

模板中必须为子分区定义名称，格式为`分区名_子分区名`，名称总长度不能大于64字节。


<span id="rangepartitiondesc" name="rangepartitiondesc"></span>

###### range_partition_desc

该语句用于指定范围组合分区的各分区、子分区的名称、分区列以及分区规则等。

<span id="hashsubpartsbyquantity" name="hashsubpartsbyquantity"></span>


**hash_subparts_by_quantity**

该语句用于按哈希分数批量指定哈希子分区，所有子分区名称由系统生成。

该语句不适用于存算一体分布式集群部署。



示例（单机/共享集群/分布式集群部署）

```sql
-- range-hash组合分区
create table rh_composite(a int, b int)
partition by range(a)
subpartition by hash(b)
(
partition p1 values less than(1) (subpartition sp1, subpartition sp2),
partition p2 values less than(2) (subpartition sp3, subpartition sp4)
);
-- range-list组合分区
create table rl_composite(a int, b int)
partition by range(a)
subpartition by list(b)
(
partition p1 values less than(1) (subpartition sp1 values(10), subpartition sp2 values(20)),
partition p2 values less than(2) (subpartition sp3 values(10), subpartition sp4 values(20))
);
-- range-range组合分区
create table rr_composite(a int, b int)
partition by range(a)
subpartition by range(b)
(
partition p1 values less than(1) (subpartition sp1 values less than(10), subpartition sp2 values less than(20)),
partition p2 values less than(2) (subpartition sp3 values less than(10), subpartition sp4 values less than(20))
);
```

<span id="compositelistpartitions" name="compositelistpartitions"></span>

##### composite_list_partitions

该语句用于定义列表分区的组合分区，即一级分区采用范围分区，子分区按需指定为范围、列表或哈希分区之一。

###### subpartition_by_range

该语句用于定义范围子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (range_subpartition_desc)**

该语句用于创建范围子分区的模板。

各属性定义与[range_partitions](#rangepartitions)语句中对应的描述一致。

###### subpartition_by_list

该语句用于定义列表子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (list_subpartition_desc)**

该语句用于创建列表子分区的模板。

各属性定义与[list_partitions](#listpartitions)语句中对应的描述一致。

###### subpartition_by_hash

该语句用于定义哈希子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (individual_hash_subparts)**

该语句用于创建列表子分区的模板。

各属性定义与[hash_partitions](#hashpartitions)语句中对应的描述一致。

<span id="listpartitiondesc" name="listpartitiondesc"></span>

###### list_partition_desc

该语句用于指定列表组合分区的各分区、子分区的名称、分区列以及分区规则等。



**hash_subparts_by_quantity**

该语句用于按哈希分数批量指定哈希子分区，所有子分区名称由系统生成。

该语句不适用于存算一体分布式集群部署。



示例（单机/共享集群/分布式集群部署）

```sql
-- list-hash组合分区
create table lh_composite(a int, b varchar(10))
partition by list(a)
subpartition by hash(b)
subpartitions 8
(partition p1 values(10), partition p2 values(DEFAULT));
-- list-list组合分区
create table ll_composite(a int, b int)
partition by list(a)
subpartition by list(b)
(
partition p1 values(1) (subpartition sp1 values(1), subpartition sp2 values(2)),
partition p2 values(2) (subpartition sp3 values(1), subpartition sp4 values(2))
);
-- list-range组合分区
create table lr_composite(a int, b int)
partition by list(a)
subpartition by range(b)
(
partition p1 values(1) (subpartition sp1 values less than(10), subpartition sp2 values less than(20)),
partition p2 values(2) (subpartition sp3 values less than(10), subpartition sp4 values less than(20))
);
```

<span id="compositehashpartitions" name="compositehashpartitions"></span>

##### composite_hash_partitions

该语句用于定义哈希分区的组合分区，即一级分区采用范围分区，子分区按需指定为范围、列表或哈希分区之一。

###### subpartition_by_range

该语句用于定义范围子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (range_subpartition_desc)**

该语句用于创建范围子分区的模板。

各属性定义与[range_partitions](#rangepartitions)语句中对应的描述一致。

###### subpartition_by_list

该语句用于定义列表子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (list_subpartition_desc)**

该语句用于创建列表子分区的模板。

各属性定义与[list_partitions](#listpartitions)语句中对应的描述一致。

###### subpartition_by_hash

该语句用于定义哈希子分区的分区列或子分区模板。

**SUBPARTITION TEMPLATE (individual_hash_subparts)**

该语句用于创建列表子分区的模板。

各属性定义与[hash_partitions](#hashpartitions)语句中对应的描述一致。

<span id="hashpartitiondesc" name="hashpartitiondesc"></span>

###### hash_partition_desc

该语句用于指定哈希组合分区的各分区、子分区的名称、分区列以及分区规则等。



**hash_subparts_by_quantity**

该语句用于按哈希分数批量指定哈希子分区，所有子分区名称由系统生成。

该语句不适用于存算一体分布式集群部署。



###### hash_partitions_by_quantity

该语句用于按哈希分数批量指定哈希分区，所有分区名称由系统生成。

各属性定义与[hash_partitions_by_quantity](#hashpartitionsbyquantity)语句中对应的描述一致。

示例（单机/共享集群/分布式集群部署）

```sql
-- hash-hash组合分区
create table hh_composite(a int, b varchar(10))
partition by hash(a)
subpartition by hash(b)
subpartitions 8
(partition p1, partition p2);
-- hash-list组合分区
create table hl_composite(c1 int, c2 varchar(10))
partition by hash(c1)
subpartition by list(c2)
(
partition p1(subpartition sp1 values('a')), 
partition p2 (subpartition sp3 values('d'), subpartition sp4 values(default))
);
-- hash-range组合分区
create table hr_composite(a int, b int, c int,d int)
partition by hash(a)
subpartition by range(b,c,d)
(
partition p1 (subpartition sp1 values less than(10,20,30), subpartition sp2 values less than(20,20,30)),
partition p2
);

-- 定义range子分区模板
create table hr_composite_template(a int, b varchar(10))
partition by hash(a)
subpartition by range(b)
subpartition template (subpartition sub1 values less than ('a') , subpartition sub2 values less than (MAXVALUE))
(partition p1,partition p2);

-- 定义list子分区模板
create table list_composite(a int, b varchar(10)) 
partition by hash(a) subpartition by list(b)
subpartition template(subpartition sp1 values('a'), subpartition sp2 values(default))
partitions 8;

-- 定义hash子分区模板
create table hash_composite(a int, b varchar(10))
partition by hash(a)
subpartition by hash(b)
subpartition template (subpartition sp1 tablespace yashan1, subpartition sp2 tablespace yashan2)
(partition p1, partition p2);

-- 仅部分定义了子分区，其余通过子分区模板自行创建子分区
create table hl_partcomposite(c1 int,c2 int)
partition by hash(c1)
subpartition by list(c2)
subpartition template(subpartition sp9 values(2))
(partition p1 (subpartition sp11 values(1)),
 partition p2,
 partition p3);
```

<span id="consistenthashpartitions" name="consistenthashpartitions"></span>

##### consistent\_hash\_partitions

该语句仅可用于为存算一体分布式集群部署中的[分布表](#shardtable)创建分区，即表示建立分布表的数据分片（Chunk）规则，且分布表仅可使用该语句创建分区。

###### CONSISTENT

本关键字可省略，表示为分布表所创建分区必须指定为一致性哈希分区。

###### column

指定分区键对应的列，包含多个列时以`,`分隔。分布表的数据分片规则即为按此分区键对应的列或列组合进行哈希分配。

YashanDB对分区键存在如下约束要求：

*   列需符合类型要求，不能为LOB、JSON、RAW、ST_GEOMETRY、ROWID及UROWID类型。
*   如果表上定义了主键列，则分区键必须为主键列的其中一项或多项。
*   如果表上未定义主键列，则分区键可以为任意项。

###### individual\_partition\_clause

该语句用于逐个指定哈希分区，语法定义与[individual\_partition\_clause](#individualpartitionclause)类似，但无法自定义指定表空间属性，即在定义分布表的分区时分区只能使用表所在的表空间。

使用本方法逐个指定分区时，分区数量必须与当前存算一体分布式集群部署环境中的Chunk总数量完全一致，否则返回错误。

###### hash\_partitions\_by\_quantity

该语句用于按哈希分数批量指定哈希分区，语法定义与[hash\_partitions\_by\_quantity](#hashpartitionsbyquantity)类似，但无法自定义指定表空间属性，即在定义分布表的分区时分区只能使用表所在的表空间。

**hash_partition_quantity**

用于哈希分区的数量，可以指定为AUTO关键字或任意数值。不论指定为何值，哈希分区的数量始终会与Chunk总数量保持一致。

> **Note**: 
>
> Chunk总数量 = 建库参数USERS_DATASPACE_SCALE_OUT_FACTOR * 建库时DN组个数，建库参数USERS_DATASPACE_SCALE_OUT_FACTOR在安装过程中配置且后续不可修改，可咨询数据库管理员获得该参数的值。

示例（存算一体分布式集群部署）

```sql
-- 重新创建销售信息表，且要求按年和月的组合哈希分配数据到各节点中(以Chunk总数量为21为例)
DROP TABLE IF EXISTS sales_info;
CREATE TABLE sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
    PARTITION BY CONSISTENT HASH (year,month) 
(PARTITION p1, PARTITION p2, PARTITION p3, PARTITION p4, PARTITION p5, PARTITION p6, PARTITION p7, 
PARTITION p8, PARTITION p9, PARTITION p10, PARTITION p11, PARTITION p12, PARTITION p13, PARTITION p14, PARTITION p15, 
PARTITION p16, PARTITION p17, PARTITION p18, PARTITION p19, PARTITION p20, PARTITION p21);

-- 通过AUTO关键字指定hash分区数量
DROP TABLE IF EXISTS sales_info;
CREATE TABLE sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY CONSISTENT HASH (year,month) 
PARTITIONS AUTO;

-- 通过数值指定hash分区数量
DROP TABLE IF EXISTS sales_info;
CREATE TABLE sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY CONSISTENT HASH (year,month) 
PARTITIONS 7;
 
INSERT INTO sales_info VALUES ('2001','01','0201','11001',30,500,'0201010011');
INSERT INTO sales_info VALUES ('2015','11','0101','11001',20,300,'');
INSERT INTO sales_info VALUES ('2021','10','0101','11001',20,300,'');
INSERT INTO sales_info VALUES ('2000','12','0102','11001',20,300,'');
INSERT INTO sales_info VALUES ('2015','03','0102','11001',20,300,'');
INSERT INTO sales_info VALUES ('2021','05','0101','11001',40,600,'');
COMMIT;
 
-- 按分区检索sales_info数据
select year,month,product,amount from sales_info partition (SYS_P1);
YEAR  MONTH PRODUCT        AMOUNT
----- ----- --------- -----------
2000  12    11001             300
2015  03    11001             300
```

<span id="consistenthashwithsubpartitions" name="consistenthashwithsubpartitions"></span>

##### consistent_hash_with_subpartitions

该语句用于为[分布表](#shardtable)创建哈希分区的组合分区，且分布表仅可使用该语句创建组合分区，仅适用于存算一体分布式集群部署。

本语句语法与[composite_hash_partitions](#compositehashpartitions)的描述类似，但不能为分区指定表空间属性。

示例（存算一体分布式集群部署）

```sql
-- 重建上例中的sales_info表，为其中一个分区创建range子分区
DROP TABLE IF EXISTS sales_info;
CREATE TABLE sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY CONSISTENT HASH (year,month) 
SUBPARTITION BY RANGE(quantity)
(
PARTITION p1 (SUBPARTITION sp1 VALUES LESS than(30), SUBPARTITION sp2 VALUES LESS than(60)),
PARTITION p2, PARTITION p3, PARTITION p4, PARTITION p5, PARTITION p6, PARTITION p7, PARTITION p8, 
PARTITION p9, PARTITION p10, PARTITION p11, PARTITION p12, PARTITION p13, PARTITION p14, PARTITION p15, PARTITION p16, PARTITION p17, PARTITION p18, PARTITION p19, PARTITION p20, PARTITION p21
);

INSERT INTO sales_info VALUES ('2001','01','0201','11001',10,500,'0201010011');
INSERT INTO sales_info VALUES ('2015','11','0101','11001',20,300,'');
INSERT INTO sales_info VALUES ('2021','10','0101','11001',30,300,'');
INSERT INTO sales_info VALUES ('2000','12','0102','11001',40,300,'');
INSERT INTO sales_info VALUES ('2015','03','0102','11001',50,300,'');
INSERT INTO sales_info VALUES ('2021','05','0101','11001',60,600,'');
COMMIT;

-- 按组合分区检索sales_info数据
select year,month,product,quantity,amount
from sales_info subpartition (sp2);
YEAR  MONTH PRODUCT      QUANTITY      AMOUNT
----- ----- --------- ----------- -----------
2000  12    11001              40         300
2015  03    11001              50         300
```

<span id="lobclause" name="lobclause"></span>

#### **lob\_clause**

该语句用于指定LOB/JSON类型列字段的存储属性，同时指定多列用`,`分隔。

- KEEP_DUPLICATES/DEDUPLICATE, CHUNK integer, NOCACHE/CACHE, LOGGING/NOLOGGING, NOCOMPRE/COMPRESS(LOW|MEDIUM|HIGH) 等lob参数仅为语法兼容， tablespace space_name参数在分区lob以及二级分区lob的lob子句中仅为语法兼容。  
- chunk integer中integer取值范围为`(0,32K]`。  
- hash分区与二级分区的lob子句仅能指定TABLESPACE space_name参数。

##### BASICFILE|SECUREFILE

该语句用于语法兼容，无实际含义。

##### TABLESPACE

指定LOB/JSON数据进行行外存储时的表空间，不指定该属性时LOB/JSON数据存储在表所在表空间中。

###### DISABLE STORAGE IN ROW

指定对于LOB/JSON列字段，无论其大小，都执行行外存储，即为其创建新的存储空间，而不是与行中的其他列字段数据存储在一起。

###### ENABLE STORAGE IN ROW

指定将LOB/JSON列字段的数据存放在行内空间，即与行中的其他列字段数据存储在一起。但当该数据大小超过一定的行内限制时，即使指定了ENABLE STORAGE IN ROW，仍进行行外存储。对于HEAP表，该限制是4000字节；对于TAC/LSC表，该限制是32000字节。

> **Note**: 
>
> 数据在存储时会产生一些内部元信息，行存表字节限制包括这部分元数据所占空间，列存表则不包括。

示例（单机/共享集群/分布式集群部署）

```sql
-- 创建一张员工信息表，其中introduce为超长文本的LOB型字段
CREATE TABLE employees1(area CHAR(2),
branch CHAR(4),
employee_info JSON,
introduce CLOB)
LOB(introduce,employee_info) STORE AS (TABLESPACE yashan ENABLE STORAGE IN ROW);
  
-- 插入两行数据，其中第一行数据里的introduce因为数据长度小于3988字节，采用的是行内存储，而第二行数据则采用的是行外存储，json字段长度小于3988字节，采用的是行内存储。
INSERT INTO employees1 VALUES ('04','0401',
JSON('{"employee_no":"0401010008","employee_name":"Sam1","sex":"1","entire_date":"SYSDATE-3"}'),
'深圳计算科学研究院是深圳市人民政府2018年11月批准建设的十大基础研究机构之一');
INSERT INTO employees1 VALUES ('04','0401',
JSON('{"employee_no":"0401010008","employee_name":"Sam2","sex":"1","entire_date":"SYSDATE-3"}'),
lpad('深圳',4000,'深圳'));
COMMIT;
 
SELECT JSON_FORMAT(employee_info), LENGTHB(introduce) FROM employees1;
JSON_FORMAT(EMPLOYEE                                                LENGTHB(INTRODUCE) 
---------------------------------------------------------------- --------------------- 
{"sex":"1","employee_no":"0401010008","entire_date":"SYSDATE-3","employee_name":"Sam1"}                   108
{"sex":"1","employee_no":"0401010008","entire_date":"SYSDATE-3","employee_name":"Sam2"}                 12000
```

<span id="loggingclause" name="loggingclause"></span>

#### logging\_clause

该语句用于指定表的记录日志的方式。

- LOGGING：默认值，表示对该表的所有操作均记录日志。主备环境中，所有表必须为该属性。

- NOLOGGING：表示仅记录必要的redo和undo日志，建议只在数据迁移场景打开此属性。以下表无法设置为该属性：

    - 临时表

    - 存在UDT、内置UDT（例如XMLTYPE、ST_GEOMETRY、BOX2D）列的表

该语句不适用于存算一体分布式集群部署。



指定为NOLOGGING的表具备以下特征：

- DML操作仅支持插入数据和导入数据，且插入数据时只会记录必要的redo和undo（例如segment相关的redo），其他redo会被忽略。

- 不能执行并发操作、回滚操作、在线创建索引（[CREATE INDEX ONLINE](./CREATE INDEX)）或在线重建索引（[ALTER INDEX REBUILD ONLINE](./ALTER INDEX)）。

- 执行全库闪回时，无法恢复NOLOGGING属性的表。

- 数据库重启时会将NOLOGGING表标记为corrupted，阻止对其进行除DROP和TRUNCATE外的任何操作。

- 如果一个事务失败，该事务内所有执行过插入数据操作的NOLOGGING表都会被标记为corrupted。

- NOLOGGING属性的LSC表使用[bulkload模式](./LOAD DATA)导入数据时，不受NOLOGGING属性影响（性能无变化，失败也不会被标记为corrupted）。

- NOLOGGING属性的TAC表不允许修改列和删除主键约束。

- TRUNCATE此类NOLOGGING表时会将其属性修改为LOGGING。

- 在共享集群/分布式集群部署中，如果发生实例启停、实例加入或共享集群故障，会把NOLOGGING表都标记为corrupted。

- 在共享集群/分布式集群部署中，设置表的NOLOGGING属性会绑定实例（可通过GV$DICT_CACHE视图的NOLOGGING_INSTANCE_ID字段查看所绑定的实例ID），下列操作只能在被绑定的实例上执行：

    - 向NOLOGGING表插入数据。

    - 将表从NOLOGGING属性变更为LOGGING。



<span id="parallelclause" name="parallelclause"></span>

#### parallel\_clause

该语句用于指定后续查询该表的默认并行度，可省略，省略表示默认不并行。

PARALLEL属性的表存在如下特征：

- 实际执行查询操作时，并行度生效优先级如下：

    - [hint](../通用SQL语法/hint.md)指定的并行度优先级最高。

    - 配置参数[DEGREE_OF_PARALLEL](../../../参考手册/配置参数.md#degreeofparallel) = 1（默认值）时，表的并行度属性优先级高于DEGREE_OF_PARALLEL。
    
    - DEGREE_OF_PARALLEL > 1时，DEGREE_OF_PARALLEL的优先级高于表的并行度属性。

- 设置为PARALLEL 1时，将直接视作未设置表的并行属性。

- insert into T2 select T1时，如果T1、T2都设置了并行度，则T2插入时也采用并行。

- 是否进行并行扫描，与hint指定并行的限制保持一致。

##### NOPARALLEL

parallel属性指定为NOPARALLEL表示不并行。

##### PARALLEL (INTEGER)

parallel属性指定为PARALLEL (INTEGER)表示并行，即查询该表执行全表扫描时默认使用并行扫描。

**integer**

并行度值，取值范围[1,服务器CPU核数\*2]，可省略，省略则并行度取值为CPU核数\*0.5。

示例

```sql
CREATE TABLE area_p8
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL) PARALLEL 8;

CREATE TABLE area_np
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL) NOPARALLEL;
```

<span id="cacheclause" name="cacheclause"></span>

#### cache\_clause

语法兼容，无实际含义。

<span id="readonlyclause" name="readonlyclause"></span>

#### readonly\_clause

语法兼容，无实际含义。

<span id="inmemoryclause" name="inmemoryclause"></span>

#### inmemory\_clause

语法兼容，无实际含义。

<span id="tablecompression" name="tablecompression"></span>

#### table\_compression

语法兼容，无实际含义。

<span id="nestedtableclause" name="nestedtableclause"></span>

#### nested_table_clause

该语句用于定义和创建嵌套表，YashanDB中的嵌套表被用于实现与含有Nested Table类型的UDT（用户自定义类型）列的存储，详见数据类型中关于[用户自定义类型](../数据类型/用户自定义类型)的描述，本语句不适用于共享集群/分布式集群部署。

##### nested_item

指定嵌套表对应的列字段，或列字段属性（该属性为Nested Table类型）。

##### COLUMN_VALUE

当出现多层嵌套表（列字段为一个行成员包含Nested Table类型的Nested Table集合）时，对于里层的嵌套表使用COLUMN_VALUE关键字来表示nested_item。

##### LOCAL|GLOBAL

当主表为分区表时，使用LOCAL关键字指定嵌套表也为与主表一一对应的分区，使用GLOBAL关键字指定嵌套表不分区，省略关键字时默认为LOCAL。

##### storage_table

指定嵌套表的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

对于多层嵌套表，在名称后接(nested_table_clause)定义里层的嵌套表信息。

##### TABLESPACE

为嵌套表指定独立的表空间，省略时默认嵌套表存储在与主表相同的表空间。

示例（单机部署）

```sql
DROP TABLE IF EXISTS area_intro;
DROP TABLE IF EXISTS province_intro;
CREATE OR REPLACE TYPE tb_type AS TABLE OF CHAR(10);
/
CREATE OR REPLACE TYPE obj_type AS OBJECT(city_id INT, branch_list tb_type);
/

-- 创建单层嵌套表
CREATE TABLE province_intro (id INT, citys tb_type, city_intro obj_type)
NESTED TABLE citys STORE AS nt_province_citys TABLESPACE yashan
NESTED TABLE city_intro.branch_list STORE AS nt_province_branch;

-- 创建多层嵌套表
CREATE OR REPLACE TYPE tb_type_city AS TABLE OF tb_type;
/
CREATE TABLE area_intro(id INT, provinces tb_type_city)
NESTED TABLE provinces STORE AS nt_area_provinces(
    NESTED TABLE COLUMN_VALUE STORE AS nt_nt_province_citys);
```

<span id="tableencryptionclause" name="tableencryptionclause"></span>

#### table_encryption_clause

该语句用于指定表的加密属性，本语句可省略，省略表示创建的表不加密。

表加密使用规则如下：

- 在单机/共享集群/分布式集群部署中，创建加密表之前必须先完成密钥管理相关配置，包括创建钱包、开启钱包、设置主密钥等，具体操作请查阅[配置钱包](../../../产品安全/加密支持/存储加密/密钥管理.md#configuringwallet)。
- sys用户不可进行表加密。
- 表加密属性一经指定无法修改。
- 如果同时使用表加密和表空间加密，目标将采用表加密算法。

**ENCRYPT**

对表开启加密功能。指定ENCRYPT时，表示创建加密表。

**USING encryption_algorithm**

该语句用于指定加密算法，支持AES128和SM4，可省略，省略时默认采用AES128算法。

> **Note**:
>
> 如需使用国密算法SM4、数据透明加密相关的密钥管理功能，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备.md)检查并确保服务器系统中已安装符合要求的工具。

示例

```sql
-- 创建加密表并指定使用SM4加密算法
DROP TABLE IF EXISTS encrypt_area;
CREATE TABLE encrypt_area
(area_no CHAR(2), 
area_name VARCHAR2(60), 
DHQ VARCHAR2(20)) 
ENCRYPT USING 'SM4';

-- 创建加密表，但不指定加密算法
DROP TABLE IF EXISTS encrypt_branches;
CREATE TABLE encrypt_branches
(branch_no CHAR(4), 
branch_name VARCHAR2(200), 
area_no CHAR(2), 
address VARCHAR2(200)) 
ENCRYPT;
```

<span id="objectaffinityclause" name="objectaffinityclause"></span>

#### object_affinity_clause

在共享集群数据库环境中，该语句用于将表和分区对象的数据资源元信息指定到具体的数据库实例上。当业务访问对象数据块时，减少了数据块元信息访问的网络开销，提升了数据访问效率。

- default：表示将对象资源亲和到当前执行`CREATE TABLE`语句的实例。
- INSTANCE instance_number：表示将对象资源亲和到指定集群实例。
- auto：将对象资源按哈希算法自动分配到集群实例。

使用资源亲和特性时，需遵循如下使用约束：

- 当前只支持指定表、表分区的亲和属性，不支持设置二级分区的亲和策略，二级分区的亲和策略与一级分区亲和策略一致。
- 不支持临时表设置亲和属性。
- 不支持修改对象的亲和属性。
- 当指定表亲和属性时，对应表下的索引、LOB、分区默认继承表的亲和属性。
- 当指定表分区亲和属性时，对应分区的本地分区索引、LOB分区默认继承当前分区的亲和属性。
- TRUNCATE TABLE后，后续数据插入产生新的SEGMENT对象自动继承表的亲和属性。

示例（共享集群部署）

```sql
-- 查看集群实例的信息
select STATUS,INSTANCE_NUMBER,INSTANCE_NAME from gv$instance where DATABASE_STATUS = 'ACTIVE';

STATUS        INSTANCE_NUMBER INSTANCE_NAME
------------- --------------- ----------------------------------------------------------------
OPEN                        1 yasdb1
OPEN                        2 yasdb2

-- 创建表并将对象亲和到对应的instance_number
create table test_affinity(a int) object affinity instance 1;
insert into test_affinity values(1);
commit;

-- 通过视图查看系统资源亲和信息
select * from v$grc_affinity_policy;

       DATA_OBJECT_ID POLICY      MASTER CURRENT_MASTER PREVIOUS_MASTER   STATUS
--------------------- --------- -------- -------------- --------------- --------
                  960 AFFINITY         1              1                        0
                  961 AFFINITY         2              2                        0
                 2919 AFFINITY         1              1                        0

3 rows fetched.
```

<span id="lsctableproperties" name="lsctableproperties"></span>

### lsc_table_properties

该语句用于定义LSC表的专有属性，这些属性只可以在创建LSC表时指定，否则报错。

#### compression_clause

定义LSC表的压缩属性，同[列字段定义的压缩属性](#compressionclause)描述，系统按此设置对所有列数据进行压缩存储。

<span id="tablesortclause" name="tablesortclause"></span>

#### table_sort_clause

本语句用于定义LSC表的排序键，其中排序列可以是表中的任意列（可组合），不指定本语句时，默认以第一列作为排序键，且默认值为NULLS FIRST ASC。

YashanDB在存储LSC表数据时，会自适应的按照一定的排序粒度对存储进行排序。通过本语句所指定的排序键仅作为加速属性使用，即当对表执行查询、更新或删除等操作时，如过滤条件中包含已定义的排序键，系统将在存储层进行排序加速计算，从而获得更优的查询性能。

##### column_name

本语句用于指定表的排序列，排序列不允许为如下类型：

- CLOB
- BLOB
- NCLOB
- BIT
- ROWID
- CURSOR
- JSON
- UDT
- RAW

##### NULLS (FIRST|LAST)

本语句用于指定空值的排列位置，NULLS FIRST表示空值排列在最前，NULLS LAST表示空值排列在最后。当未指定此语句时，对升序排列缺省为NULLS LAST，对降序排列缺省为NULLS FIRST。 

##### ASC|DESC

本语句用于指定排序方式，ASC表示升序，DESC表示降序，省略则默认为升序。

##### SCOL

默认LSC表可变数据（MCOL）也会使用排序键，如可变数据（MCOL）不需要点查加速，在创建时可指定仅针对稳态数据（SCOL）使用排序键，也可以通过[ALTER TABLE](./ALTER TABLE)语句开启或关闭可变数据（MCOL）的排序键。

> **Note**: 
>
> 可变数据的排序键会降低导入性能，生产环境中的存量数据建议先导入然后再打开可变数据的排序键，或直接通过Bulkload模式导入成稳态数据。

示例（LSC表）

```sql
-- 创建LSC表，压缩且按指定列排序存储
DROP TABLE IF EXISTS finance_info;
CREATE TABLE finance_info
(year CHAR(4) NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4),
revenue_total NUMBER(10,2),
cost_total NUMBER(10,2),
fee_total NUMBER(10,2)
)
COMPRESSION lz4 HIGH
ORDER BY (year,month,branch);
```

<span id="mcolttlclause" name="mcolttlclause"></span>

#### mcol_ttl_clause

该语句用于定义LSC表的可变数据最大生命周期。

在YashanDB中，对LSC表插入的数据将先作为可变数据（MCOL）存储，满足一定条件后触发后台线程将可变数据转为稳态数据（SCOL，不可变数据）进入数据桶（bucket，对象存储目录）。

此值保证用户插入数据在可变数据区内至少保留1/2 MCOL TTL时间，最多则保留MCOL TTL时间。

指定了可变数据最大生命周期后，系统将把该值和数据量一起作为触发转换线程的条件，让用户可以根据自身业务情况设定更合理的转换时间。

**timestamp**

生命周期数值，该值须遵循INTERVAL YEAR TO MONTH或INTERVAL DAY TO SECOND数据类型的格式表述，例如`'1' YEAR(9)`、`'30:59.9' MINUTE TO SECOND(6)`，具体请参考[日期时间型](../数据类型/日期时间型)中这两种类型的描述。 

示例（LSC表）

```sql
-- 创建可变数据生命周期为1个月的LSC表，即在满1个月时，MCOL数据将被转换为SCOL数据
DROP TABLE IF EXISTS finance_info;
CREATE TABLE finance_info
(year CHAR(4) NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4),
revenue_total NUMBER(10,2),
cost_total NUMBER(10,2),
fee_total NUMBER(10,2)
)
COMPRESSION lz4 HIGH
ORDER BY (year,month,branch)
MCOL TTL '1' MONTH;
```

<span id="transformerclause" name="transformerclause"></span>

#### transformer_clause

LSC表默认拥有转换、合并和生成AC数据的后台数据转换能力。

在某些情况下，如果希望建表时就设置某些后台数据转换能力，可以使用此语法进行关闭或打开。建表完成后也可再使用[ALTER TABLE](./ALTER TABLE)语句对后台数据转换能力进行开启或关闭。

示例（LSC表）

```sql
-- 创建一个关闭合并能力的LSC表
drop table if exists lsc_compact_disable;
create table lsc_compact_disable(x int) disable compact;
```

<span id="mcolabilityclause" name="mcolabilityclause"></span>

#### mcolability_clause

建表时指定是否开启MCOL，如未指定则根据配置项LSC_MCOL_ENABLED决定是否开启MCOL，配置项默认关闭MCOL。

MCOL比较偏向于TP业务，拥有良好的并发小事务处理能力，但在数据压缩率以及批量导入和查询性能上不如SCOL。

<span id="rowmovementclause" name="rowmovementclause"></span>

### row\_movement\_clause

该语句用于指定创建的表中的行数据是否可以物理移动，HEAP表和TAC表省略则默认为DISABLE ROW MOVEMENT，LSC表省略则默认为ENABLE ROW MOVEMENT。

在YashanDB中，默认行的Rowid是不变化的，但在某些特殊场景，例如对分区表的分区键值进行更新操作，导致其所在行需要从当前分区迁移到另一个分区中，行的Rowid将发生变更，这种变动在表ENABLE ROW MOVEMENT时是被允许的，且YashanDB在此情况下会保证事务的强一致性，避免出现漏更新（lost update）。

开启row movement后，并发事务中可能会触发语句重启（restart statement，回滚之前的操作并重新执行DML事务），影响并发性能 ，因此在正常情况下不建议开启此开关，在可预知会发生行迁移的业务场景中（例如闪回数据），可通过[ALTER TABLE](./ALTER TABLE)语句临时打开，并在结束后关闭。

存算一体分布式集群部署中仅有分区表跨分区更新或LSC表中稳态数据更新时需要ENABLE ROW MOVEMENT，跨分区更新场景中仅DN节点内进行跨分区更新，无法更新分区键。

示例（单机/共享集群/分布式集群部署）

```sql
-- 创建一张ENABLE ROW MOVEMENT的分区表
DROP TABLE IF EXISTS orders_info_rowmove;
CREATE TABLE orders_info_rowmove (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id number)
PARTITION BY RANGE (id )
(PARTITION p1 VALUES LESS THAN (800),
PARTITION p2 VALUES LESS THAN (1800),
PARTITION p3 VALUES LESS THAN (2800))
ENABLE ROW MOVEMENT;
```
