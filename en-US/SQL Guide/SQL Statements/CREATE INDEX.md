## General Description

CREATE INDEX is used to create an index object on a specified table. In YashanDB, various types of indexes can be created, such as:

- [UNIQUE INDEX](#UNIQUE)
- [COLUMNAR INDEX](#COLUMNAR)
- [FUNCTION INDEX](#columnexpression)
- [REVERSE INDEX](#REVERSE)
- [PARTITION INDEX](#localindexclause)
- [RTree index](#RTREE): Only used for ST_Geometry type columns

## Statement Definition

**create index::=**

```ebnf+diagram
syntax::= CREATE [UNIQUE|COLUMNAR|RTREE] INDEX [schema"."] index_name ON table_name "(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_attr_clause]
```

**[index_expr](#indexexpr)::=**

```ebnf+diagram
syntax::= column_name|column_expression
```

**[index_attr_clause](#indexattrclause)::=**

```ebnf+diagram
syntax::= (TABLESPACE (tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|storage_clause|(VISIBLE|INVISIBLE)|(USABLE|UNUSABLE)|local_index_clause|GLOBAL|ONLINE|(NOPARALLEL|PARALLEL [integer])|(NOCOMPRESS|COMPRESS [integer|advanced])|(LOGGING|NOLOGGING)|(NOREVERSE|REVERSE)|readonly_clause|inmemory_clause|COMPUTE STATISTICS) 
{" " (TABLESPACE (tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|storage_clause|(VISIBLE|INVISIBLE)|(USABLE|UNUSABLE)|local_index_clause|GLOBAL|ONLINE|(NOPARALLEL|PARALLEL [integer])|(NOCOMPRESS|COMPRESS [integer|advanced])|(LOGGING|NOLOGGING)|(NOREVERSE|REVERSE)|readonly_clause|inmemory_clause|COMPUTE STATISTICS)}
```

**[storage_clause definition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/storage_clause)**

**[local_index_clause](#localindexclause)::=**

```ebnf+diagram
syntax::= LOCAL [store_in_clause|index_partition_clause|index_comp_partition_clause]
```

**[readonly\_clause](#readonlyclause)::=**

```ebnf+diagram
syntax::= READONLY | READWRITE
```

**[inmemory\_clause](#inmemoryclause)::=**

```ebnf+diagram
syntax::= INMEMORY | NO INMEMORY
```

**[store_in_clause](#indexhashpartitionclause)::=**

```ebnf+diagram
syntax::= STORE IN "(" ((tablespace) {"," (tablespace)}) ")"
```

**[index_partition_clause](#indexpartitionclause)::=**

```ebnf+diagram
syntax::= "(" ((PARTITION [partition] [index_partition_attr_clause]) 
{"," (PARTITION [partition] [index_partition_attr_clause])}) ")"
```

**[index_partition_attr_clause](#indexpartitionattrclause)::=**

```ebnf+diagram
syntax::= (TABLESPACE ( tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|USABLE|UNUSABLE) 
{" " (TABLESPACE ( tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|USABLE|UNUSABLE)}
```

**[index_comp_partition_clause](#indexcomppartitionclause)::=**

```ebnf+diagram
syntax::=[store_in_clause] "(" PARTITION [partition] [index_partition_attr_clause] [USABL] [index_subpartition_clause] {"," PARTITION [partition] [index_partition_attr_clause] [index_subpartition_clause]} ")"
```

**[index_subpartition_clause](#indexsubpartitionclause)::=**

```ebnf+diagram
syntax::= STORE IN "(" (tablespace) {"," (tablespace)} ")" | "(" (SUBPARTITION [subpartition] [TABLESPACE tablespace] [USABLE|UNUSABLE]) {"," (SUBPARTITION [subpartition] [TABLESPACE tablespace] [USABLE|UNUSABLE])} ")"
```

<span id="UNIQUE" name="UNIQUE" class="yaslink"></span>

### 1. UNIQUE

This statement is used to specify that the created index is a unique index, meaning that the values in the indexed column fields must be unique. If there are duplicate values in that column of the table, the creation of the unique index will fail.

The LSC table must specify this keyword.

In an ISC Distributed Cluster Deployment, the partition column must be a subset of the unique index key columns, otherwise an error will occur.

***Example***

```sql
-- Create a unique index idx_orders_info_1 on the id and area columns in the orders_info table
CREATE UNIQUE INDEX idx_orders_info_1 ON orders_info (id, area);
```

<span id="COLUMNAR" name="COLUMNAR" class="yaslink"></span>

### 2. COLUMNAR

This statement is used to specify that the created index is a columnar index, meaning that the data content of the index is stored in a column-oriented manner, and the index itself is unordered.

Columnar indexes have the following restrictions:

-  When specified as a columnar index, it cannot also be specified as a [FUNCTION INDEX](#columnexpression) or [REVERSE INDEX](#REVERSE), and it cannot be created (CREATE) or rebuilt (ALTER INDEX) online (ONLINE).
- Columnar indexes can only be created on standalone HEAP tables.

***Example*** for Standalone Deployment Heap tables

```sql
-- Create a columnar index colidx_orders_info_1 on the product_no column in the orders_info table
CREATE COLUMNAR INDEX colidx_orders_info_1 ON orders_info (product_no);
```

<span id="RTREE" name="RTREE" class="yaslink"></span>

### 3. RTREE

This statement is used to specify the creation of an RTree index.

RTree indexes have the following restrictions:

- RTree indexes are only applicable to HEAP tables in Standalone Deployment and YAC Deployment.

- RTree indexes can only be created for columns of [ST_Geometry type](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/ST_GEOMETRY), and each RTree index can only specify one column.

-  When specified as an RTree index, it cannot also be specified as a [FUNCTION INDEX](#columnexpression) or [REVERSE INDEX](#REVERSE), and it cannot be created (CREATE) or rebuilt (ALTER INDEX) online (ONLINE).

- When using RTree indexes, the transaction isolation level cannot be set to [SERIALIZABLE](SET TRANSACTION).

***Example*** for Standalone Deployment Heap tables and YAC Deployment Heap tables

```sql
-- Create an RTree index idx_rtree_shp on the shp column in the geom_test table, where the shp column's data type is ST_Geometry
CREATE TABLE geom_test(id INT, geom_type VARCHAR(32), shp st_geometry);
CREATE RTREE INDEX idx_rtree_shp ON geom_test (shp);
```

### 4. index\_name

This statement specifies the name of the index to be created. It cannot be omitted and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

### 5. table\_name

This statement specifies the table on which to create the index. During the index creation process, an exclusive lock will be applied to that table, but if ONLINE is specified, this operation will not block concurrent DML transactions on the table.

<span id="indexexpr" name="indexexpr" class="yaslink"></span>

### 6. index\_expr

Specifies the columns or expressions on which the index is based. When multiple combinations are specified, they are separated by commas (`,`).

The rules are as follows:

- [LOB type](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/大对象型) columns cannot be used as index columns.

- Only RTree indexes can be created on columns of [ST_Geometry type](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/ST_GEOMETRY), and each RTree index can only specify one column.

- Only BTREE indexes for equality queries can be created on encrypted columns.

#### 6.1. column\_name

Specifies a named column as the index column.

<span id="columnexpression" name="columnexpression" class="yaslink"></span>

#### 6.2. column\_expression

Specifies an expression as the index column. An index containing expression-based columns is referred to as a FUNCTION index.

YashanDB supports the use of any [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) as an index column for creating FUNCTION indexes, but there are the following constraint rules:

- The result of column_expression must meet all system constraints for index columns, such as not being LOB type and the length not exceeding the index key value specification, etc.
- If there is a built-in function in column_expression, that function must be a non-aggregate function that can return deterministic and invariant results, such as SYSDATE, USERENV, SUM, etc. cannot be used.
- If there is auto-fill behavior in column_expression, it cannot be an expression with non-fixed auto-fill behavior. For example, auto-filling of month and year during date conversion cannot be established as a FUNCTION index because the system needs to use the current month and year (non-fixed values), leading to an undetermined result. However, fixed auto-fill behavior like filling seconds and zero for CHAR type can create FUNCTION indexes.
- FUNCTION expressions cannot be used on partition keys when creating partition indexes.
- User UDF cannot be included in column_expression.
- When specified as an [RTREE INDEX](#RTREE), it cannot be simultaneously specified as a FUNCTION index.
- FUNCTION indexes cannot be created for LSC tables.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- FUNCTION index can be any expression
CREATE UNIQUE INDEX idx_orders_info_2 ON orders_info (TO_CHAR(id)||order_no);
CREATE INDEX idx_orders_info_3 ON orders_info (CAST(id AS CHAR(14)),order_no);

-- When converting a string to DATE without specifying a format, it leads to the system assuming an unpredictable default format, so it cannot return an invariant result
CREATE INDEX idx_orders_info_4 ON orders_info (order_date-TO_DATE('2001-01-01'));
[1:69]YAS-04399 only pure functions can be indexed
```

### 7. DESC|ASC

Specifies the sorting method of the index column, which can be omitted, in which case the system defaults to ascending order.

***Example*** for Heap tables and TAC tables

```sql
-- Specify the ascending and descending order of the index columns
CREATE INDEX idx_orders_info_4 ON orders_info (order_date DESC, TO_NUMBER(order_no) ASC);
```

<span id="indexattrclause" name="indexattrclause" class="yaslink"></span>

### 8. index\_attr\_clause

This statement is used to specify various attributes of the index.

#### 8.1. TABLESPACE

This statement is used to specify the tablespace of the index. If omitted, the current user's tablespace is used by default. Users cannot specify the tablespace for indexes and index partitions built on sharded tables; the tablespace of these indexes and index partitions will automatically be consistent with that of the table.

*   tablespace_name: Specify an existing tablespace.
*   DEFAULT: Specify the default tablespace, which is the tablespace of the current user.

The tablespace for indexes created on temporary tables can only be the system's default temporary tablespace and cannot be specified.

#### 8.2. INITRANS/PCTFREE/storage\_clause

This statement is used to specify the storage properties of the index. If omitted, INITRANS/PCTFREE defaults to 2/8; for other storage properties, please refer to the general SQL syntax [storage](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/storage_clause) description.

#### 8.3. VISIBLE|INVISIBLE

This statement is used to specify whether the created index can be utilized by the optimizer (Optimizer). If omitted, it defaults to VISIBLE.

#### 8.4. USABLE|UNUSABLE

This statement is used to specify whether the created index is usable. If omitted, it defaults to USABLE.

#### 8.5. GLOBAL

This statement is used to explicitly specify that the created index is a global index. If the GLOBAL keyword is not specified, YashanDB also defaults to creating a global index. Specifying the GLOBAL keyword for sharded tables will result in an error.

<span id="ONLINE" name="ONLINE" class="yaslink"></span>

#### 8.6. ONLINE

This statement is used to specify whether concurrent DML operations are allowed during the index creation process. If omitted, it defaults to not allowing it.

Online index creation has the following restrictions:

-  Online index creation is not applicable to ISC Distributed Cluster Deployment.
- Online index creation is not permitted for LSC tables and temporary tables.
- Online index creation is not allowed for [COLUMNAR INDEX](#COLUMNAR) and [RTREE INDEX](#RTREE).
- In YAC mode, ongoing create/rebuild index online operations will fail upon experiencing instance start and stop events.

<span id="parallel" name="parallel" class="yaslink"></span>

#### 8.7. NOPARALLEL|PARALLEL

This statement is used to set the degree of parallelism for index creation. NOPARALLEL indicates no parallelism; if the degree of parallelism is not specified, it defaults to half the number of CPU cores.

Index creation for LSC tables does not allow parallelism, i.e., integer cannot be greater than 1.

**integer**

The degree of parallelism, acceptable values range from [1, server CPU core count * 2]. It can be omitted. If omitted and the current table has a data volume greater than 1G but less than the current DATA_BUFFER_SIZE parameter value, it defaults to half the number of CPU cores.

***Example*** for Standalone Deployment Heap tables and Standalone Deployment TAC tables

```sql
-- Create index online with parallelism
DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) ONLINE PARALLEL 2;
```

#### 8.8. NOCOMPRESS|COMPRESS

This statement is for syntax compatibility and has no actual meaning.

#### 8.9. LOGGING|NOLOGGING

This statement is used to specify the logging properties of the index creation. It can be omitted; if omitted, it defaults to logging.

A nologging index can only be created on nologging tables.

***Example*** for Heap tables and TAC tables

```sql
CREATE TABLE logging_table(c1 INT);

CREATE INDEX logging_index ON logging_table(c1);

-- A nologging index can only be created on a nologging table
CREATE INDEX nologging_index ON logging_table(c1) NOLOGGING;
YAS-02367 create nologging index is not allowed for logging table
```

<span id="REVERSE" name="REVERSE" class="yaslink"></span>

#### 8.10. NOREVERSE|REVERSE

This statement is used to specify whether the created index is a reverse index. A reverse index reverses the bytes of the indexed column when storing data, thereby reducing leaf block contention with discrete data. If omitted, it defaults to NOREVERSE.

Reverse indexes cannot be created for LSC tables.

***Example*** for Heap tables and TAC tables

```sql
DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) REVERSE;

DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) NOREVERSE;
```

<span id="readonlyclause" name="readonlyclause" class="yaslink"></span>

##### 8.10.1. readonly\_clause

For syntax compatibility, it has no actual meaning.

<span id="inmemoryclause" name="inmemoryclause" class="yaslink"></span>

##### 8.10.2. inmemory\_clause

For syntax compatibility, it has no actual meaning.

<span id="localindexclause" name="localindexclause" class="yaslink"></span>

#### 8.11. local\_index\_clause

This statement is used to create a partition index (Local Partitioned Index). Partition indexes can only be created on partition tables, and the number of index partitions and partition boundaries must be the same as that of the table.

To create a unique index, ensure that the union of the first-level partition keys and second-level partition keys is a subset of the index key.

If the statements following LOCAL are omitted, the index partitions will be created according to the following rules:

*    The number of partitions is the same as that of the table's partitions.
*    The partition boundaries are the same as those of the table.
*   The tablespace is consistent with the value specified in the INDEX.
*   The storage attributes such as INITRANS/PCTFREE are consistent with those specified in the INDEX.
*   The USABLE/UNUSABLE switches are set to USABLE.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- Create a local index 
CREATE INDEX idx_sales_info_2 ON sales_info (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL;
```

<span id="indexhashpartitionclause" name="indexhashpartitionclause" class="yaslink"></span>

##### 8.11.1. store\_in\_partition\_clause

This statement is only used to specify the tablespace for index partitions on hash partition tables. Specify multiple table partitions separated by commas, and the number must match the number of index partitions.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- Specify the partition tablespace for hash partition indexes
CREATE INDEX idx_sales_info_hash_1 ON sales_info_hash (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL STORE IN (yashan,yashan1);
```

<span id="indexpartitionclause" name="indexpartitionclause" class="yaslink"></span>

##### 8.11.2. index\_partition\_clause

Specifies the name and other properties of index partitions. Multiple partitions can be specified separated by commas, and the number must match the number of index partitions.

###### 8.11.2.1. partition\_name

Specifies the name of the index partition. If omitted, a default name will be generated by the system.

<span id="indexpartitionattrclause" name="indexpartitionattrclause" class="yaslink"></span>

###### 8.11.2.2. index\_partition\_attr\_clause

Specifies the following attributes of index partitions:

*   Tablespace: If omitted, it defaults to the tablespace specified in the INDEX. The DEFAULT value equals the tablespace name specified in the INDEX.
*   INITRANS/PCTFREE: If omitted, it defaults to the storage attribute value specified in the INDEX.
*   USABLE/UNUSABLE: If omitted, it defaults to USABLE.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- Specify the properties of index partitions
CREATE INDEX idx_sales_info_range_1 ON sales_info_range (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL (PARTITION p1 TABLESPACE DEFAULT INITRANS 2,PARTITION p2,PARTITION p3);
```

<span id="indexcomppartitionclause" name="indexcomppartitionclause" class="yaslink"></span>

##### 8.11.3. index\_comp\_partition\_clause

Specifies the names and other properties of index composite partitions. Multiple partitions can be specified separated by commas, and the number must match the corresponding table partitions.

###### 8.11.3.1. store in

Specifies secondary partition template information, applicable only to hash partitions.

<span id="indexsubpartitionclause" name="indexsubpartitionclause" class="yaslink"></span>

###### 8.11.3.2. index\_subpartition\_clause

Specifies the names and other properties of secondary index partitions, with multiple secondary partitions specified separated by commas, matching the number of corresponding table secondary partitions.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Create a secondary partition index with default names
CREATE UNIQUE INDEX idx_orders_info_sub ON orders_info(area, id) LOCAL;
```

***Example*** for Standalone Deployment and YAC Deployment and Heap tables and TAC tables

```sql
-- Create a secondary partition index with specified names
CREATE INDEX idx_sales_info_sub ON sales_info(product) LOCAL
(PARTITION ip_sales_info_1 UNUSABLE(SUBPARTITION isp_sales_info_11, SUBPARTITION isp_sales_info_12, SUBPARTITION isp_sales_info_13),
PARTITION ip_sales_info_2 USABLE (SUBPARTITION isp_sales_info_21, SUBPARTITION isp_sales_info_22, SUBPARTITION isp_sales_info_23),
PARTITION ip_sales_info_3 USABLE (SUBPARTITION isp_sales_info_31, SUBPARTITION isp_sales_info_32, SUBPARTITION isp_sales_info_33));
```

#### 8.12. COMPUTE STATISTICS

This statement is for syntax compatibility and has no actual meaning.