## General Description

`ALTER TABLE` is used to modify the structure and definition of a table in the database, as well as to perform related management operations on the table, including:

- [Renaming a table](#RENAMETO)
- [Modifying columns](#altercolumnclause)
- [Modifying partitions](#altertablepartition), [Merging partitions](#mergetablesubpartitions)
- [Modifying constraints](#altertableconstraint)
- [Modifying identity columns](#altertableidentity)
- Enabling and disabling row movement ([row movement](#rowmovementclause))
- Enabling and disabling supplemental logging ([supplemental logging](#supplementaltablelogging))
- Enabling and disabling redo logging ([nologging](#loggingclause))
- Enabling and disabling parallel ([parallel](#parallelclause))
- Space shrinking ([shrink space](#shrinkspaceclause))
- Enabling and disabling LSC table background data transformation options ([data transformer](#enablexfmrclause))
- Setting the mutable data lifecycle of LSC tables ([MCOL time to live](#mcolttlclause))
- Enabling or disabling MCOL functionality on LSC tables ([MCOL ability](#mcolabilityclause))
- Forcing transformations on LSC tables ([force xfmr](#forcexfmrclause))

Among them, according to the storage features of LSC tables, only the partition-related properties (excluding partition indexes) can be modified (including adding partitions and deleting partitions, etc.).

It is not allowed to perform `ALTER TABLE` operations on [AC objects](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Schema Objects) and the source tables of AC objects.

Statement Definition
----

**alter table::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name 
(alter_table_properties
|alter_column_clause
|alter_table_partition
|alter_table_constraint
|reclaim_table_clause
|alter_table_identity)
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
syntax::= ADD [COLUMN] "(" (column_definition|virtual_column_definition) {"," (column_definition|virtual_column_definition)} ")"
[lob_clauses]
```

**[column\_definition](#columndefinition)::=**

```ebnf+diagram
syntax::= column dataType [identity_clause] [(DEFAULT default_expr | inline_constraint | column_encryption_clause)
{" " (DEFAULT default_expr | inline_constraint | column_encryption_clause)}]
```

**virtual_column_definition::=**

```ebnf+diagram
syntax::= column_name [datatype] [VISIBLE|INVISIBLE] [GENERATED ALWAYS] AS "(" column_expression ")" [VIRTUAL]
```

For the syntax specifications of virtual columns, refer to [virtual_column_definition](./CREATE TABLE.html#virtualcoldef).

**[identity_clause](#identityclause)::=**

```ebnf+diagram
syntax::= GENERATED [ALWAYS |(BY DEFAULT [(ON NULL)])] AS IDENTITY [identity_options]
```

**[identity_options](#identityoptions)::=**

```ebnf+diagram
syntax::= ["("] [((INCREMENT BY integer)|(START WITH (integer|LIMIT VALUE))|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE)) 
{" " ((INCREMENT BY integer)|(START WITH (integer|LIMIT VALUE))|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE))}] [")"]
```

**[inline_constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)** 

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
syntax::= MODIFY (column_name (dataType|DEFAULT default_expr|identity_clause|inline_constraint) 
{" " (dataType|DEFAULT default_expr|identity_clause|inline_constraint)}) 
{"," (column_name (dataType|DEFAULT default_expr|identity_clause|inline_constraint) 
{" " (dataType|DEFAULT default_expr|identity_clause|inline_constraint)})}
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
|exchange_partition_clause
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

**[exchange_partition_clause](#exchangeclause)::=**

```ebnf+diagram
syntax::= EXCHANGE (PARTITION | SUBPARTITION) (part | subpart | FOR "(" (partition_key_value) {"," (partition_key_value)} ")" ) WITH TABLE [schema"."] table_name [ (INCLUDING | EXCLUDING) INDEXES ] [ (WITH | WITHOUT)  VALIDATION] [UPDATE [GLOBAL] INDEXES] [CASCADE]
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

**[out_of_line_constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)** 

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

**[alter\_table\_identity](#altertableidentity)::=**

```ebnf+diagram
syntax::= MODIFY identity_column_name [dataType] (identity_clause |(DROP IDENTITY))
```

<span id="altertableproperties" name="altertableproperties" class="yaslink"></span>

### 1. alter\_table\_properties

This statement is used to modify a series of properties of a table.

<span id="RENAMETO" name="RENAMETO" class="yaslink"></span>

#### 1.1. RENAME TO

This statement is used to modify the table name. The specified new name cannot be empty and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

***Example***

```sql
ALTER TABLE area RENAME TO area_new;
ALTER TABLE area_new RENAME TO area;
```

<span id="rowmovementclause" name="rowmovementclause" class="yaslink"></span>

#### 1.2. row\_movement\_clause

This statement is used to enable or disable the row movement functionality of the table, referring to the corresponding functionality description in [CREATE TABLE](CREATE TABLE).

***Example*** for Standalone Deployment Heap tables and Standalone Deployment TAC tables

```sql
-- Create a partitioned table with DISABLE ROW MOVEMENT
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
  
-- Insert data
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',1300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',2300);
COMMIT;
  
-- Query records in p1 partition
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove PARTITION(p1);
ORDER_NO          ORDER_DESC   AREA  BRANCH ORDER_DATE           SALESPERSON      ID
----------------- ------------ ----- ------ -------------------- ------------- -----
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            300
 
-- Update partition column value. This statement will throw an error when row movement is not enabled
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
YAS-02209 ROW MOVEMENT is not enabled

-- Execute enabling row movement
ALTER TABLE orders_info_rowmove ENABLE ROW MOVEMENT;
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
 
-- Re-query records in p1 partition; data that was in p2 partition has moved to this partition
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove PARTITION(p1);
ORDER_NO          ORDER_DESC   AREA  BRANCH ORDER_DATE           SALESPERSON      ID
----------------- ------------ ----- ------ -------------------- ------------- -----
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            300
20010102020001    product 001  02    0201   2020-12-06 22:55:32  0001            500

-- After updating data across partitions, it is recommended to disable row movement
ALTER TABLE orders_info_rowmove DISABLE ROW MOVEMENT;
```

***Example*** for ISC Distributed Cluster Deployment TAC tables

```sql
-- Create a TAC table with DISABLE ROW MOVEMENT
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
  
-- Insert data
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',1300);
INSERT INTO orders_info_rowmove VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',2300);
COMMIT;
  
-- Query records in the sp1 subpartition of p1 (Results may differ due to the distributed first-level partition being HASH partition)
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove SUBPARTITION(p1_sp1);
ORDER_NO          ORDER_DESC                                                       AREA  BRANCH ORDER_DATE                       SALESPERSON            ID
----------------- ---------------------------------------------------------------- ----- ------ -------------------------------- ------------- -----------
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  300
 
-- Update partition column value. This statement will throw an error when row movement is not enabled
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
YAS-02209 ROW MOVEMENT is not enabled

-- Execute enabling row movement
ALTER TABLE orders_info_rowmove ENABLE ROW MOVEMENT;
UPDATE orders_info_rowmove SET id=500 WHERE id=1300;
 
-- Re-query records in the sp1 subpartition of p1; data that was in sp2 subpartition has been moved to this partition
SELECT order_no,order_desc,area,branch,order_date,salesperson,id FROM orders_info_rowmove SUBPARTITION(p1_sp1);
ORDER_NO          ORDER_DESC                                                       AREA  BRANCH ORDER_DATE                       SALESPERSON            ID
----------------- ---------------------------------------------------------------- ----- ------ -------------------------------- ------------- -----------
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  300
20010102020001    product 001                                                      02    0201   2022-08-21                       0001                  500

-- After updating data across partitions, it is recommended to disable row movement
ALTER TABLE orders_info_rowmove DISABLE ROW MOVEMENT;
```

<span id="supplementaltablelogging" name="supplementaltablelogging" class="yaslink"></span>

#### 1.3. supplemental\_table\_logging

This statement is used to enable or disable supplemental logging properties at the table level. Table-level supplemental logging only applies to the current table. For database-level supplemental logging, refer to [ALTER DATABASE](ALTER DATABASE.html#supplementallogclauses).

In ISC Distributed Cluster Deployment, users cannot perform this operation.

To use the [YStream server](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM), supplemental logging (either at the database level or table level) must be enabled before creating the YStream server. If the YStream server is created first and then supplemental logging is enabled, an error will occur.

<span id="addsupplementalloggingclause" name="addsupplementalloggingclause" class="yaslink"></span>

##### 1.3.1. add\_supplemental\_logging\_clause

This statement is used to enable supplemental logging properties for the table. Once supplemental logging is enabled, additional information will be recorded in the redo log when DML or DDL statements are executed on the table, allowing logic log parsing tools to derive DML and DDL statements from the redo log. This is particularly true for UPDATE and DELETE statements, which will additionally record the primary key, unique key, or entire row (excluding LOB columns) data in the redo log, thus impacting database performance.

There are three types of supplemental logging:

- PRIMARY KEY: For UPDATE and DELETE redo logs, only the values of the primary key columns of the row are recorded.
- UNIQUE: For UPDATE and DELETE redo logs, only the values of the non-null unique index columns of the row are recorded.
- ALL: For UPDATE and DELETE redo logs, the values of all columns (excluding LOB type, varchar and char larger than 32K, etc.) of the row are recorded.

Supplemental logging constraints:

- The ADD SUPPLEMENTAL LOG operation cannot be executed again on a table that has already enabled supplemental logging.
- For tables with PRIMARY KEY or UNIQUE types of supplemental logging, the indexed columns that depend on supplemental logging cannot be deleted.
- Supplemental logging of the PRIMARY KEY type cannot be enabled on tables that do not have a primary key.
- Supplemental logging of the UNIQUE type cannot be enabled on tables that do not have a non-null unique index column.
- UNIQUE type supplemental logging cannot be enabled on tables with unique index columns that have [encryption](#columnencryptionclause) enabled.
- Supplemental logging cannot be enabled on TAC tables that use dictionary encoding.
- DDL statements exceeding 32K cannot be executed on tables with supplemental logging enabled.

The current enabled supplemental logging information can be viewed through the views [DBA_LOG_GROUPS](../../All Manuals/参考手册/系统视图/DBA视图/DBA_LOG_GROUPS), [ALL_LOG_GROUPS](../../All Manuals/参考手册/系统视图/ALL视图/ALL_视图), and [USER_LOG_GROUPS](../../All Manuals/参考手册/系统视图/USER视图).

***Example*** for Standalone Deployment
```sql
ALTER TABLE area ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
ALTER TABLE branches ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
```

<span id="dropsupplementalloggingclause" name="dropsupplementalloggingclause" class="yaslink"></span>

##### 1.3.2. drop\_supplemental\_logging\_clause

This statement is used to disable supplemental logging properties on the table.

***Example*** for Standalone Deployment

```sql
ALTER TABLE area DROP SUPPLEMENTAL LOG DATA;
```

<span id="shrinkspaceclause" name="shrinkspaceclause" class="yaslink"></span>

#### 1.4. shrink\_space\_clause

This statement is used to shrink the table or partition, making data storage more compact, lowering the segment's high-water mark (HWM), and releasing the freed continuous extents.

This statement is only applicable to HEAP tables.

Since shrinking data involves changes to physical locations, row movement must be enabled before executing this operation. Refer to [row movement clause](#rowmovementclause) for details.

**COMPACT**

Specifying this keyword indicates that data is only compacted for storage without releasing the freed extents.

**CASCADE**

Specifying this keyword indicates that the indexes on the table will also be shrunk as the table is shrunk. This is only for syntax compatibility and has no practical significance.

***Example*** for Heap tables

```sql
ALTER TABLE orders_info ENABLE ROW MOVEMENT;
ALTER TABLE orders_info SHRINK SPACE;
ALTER TABLE orders_info SHRINK SPACE COMPACT;
ALTER TABLE orders_info SHRINK SPACE COMPACT CASCADE;
```

<span id="lscpropertiesclause" name="lscpropertiesclause" class="yaslink"></span>

#### 1.5. lsc\_properties\_clause

This statement is only used to modify properties related to LSC tables. Executing these modifications on other types of tables will result in an error.

<span id="enablexfmrclause" name="enablexfmrclause" class="yaslink"></span>

##### 1.5.1. enable\_xfmr\_clause

This statement is used to control the background data transformation capabilities of LSC tables.

Currently, YashanDB provides the following data transformation capabilities for LSC tables:

- TRANSFORM: Mutable data in the LSC is transformed into stable data storage based on this switch, and the data in the mutable data cache is transformed and transferred to stable data storage. Disabling this switch means automatic transformation is no longer allowed. It is important to note that if transformation is not performed for an extended period, the data will remain in the mutable data area, and optimization cannot be made to the data storage format. Long-term closure is not recommended.
- COMPACT: The stable data of the LSC automatically optimizes data format based on this switch (including sorting and merging). Disabling this switch means automatic optimization is no longer allowed. It is important to note that optimization of stable data is beneficial for query performance improvement. However, additional resource consumption occurs during optimization, which can be disabled according to business needs.
- BUILD AC: The stable data of the LSC generates AC data files based on this switch. Disabling this switch stops the automatic generation of AC data files, and AC capabilities can be individually controlled based on business needs.

This clause allows multiple capabilities to be enabled or disabled simultaneously. Users can also enable or disable background data transformation capabilities globally by configuring the DATA_TRANSFORMER_ENABLED parameter.

> **Note**: 
>
> 1. TRANSFORM and COMPACT involve physical space changes. After transformation tasks are executed, the previous data before transformation will be retained for a period to meet long query needs. Once the maximum retention time is reached, this data will be cleaned up. Users can execute immediate cleaning using the [force xfmr clause](#forcexfmrclause) as needed, provided there is no impact on business.
> 2. When there are no AC objects in the system, opening the BUILD AC switch will not create AC data files.

***Example*** for LSC tables

```sql
ALTER SYSTEM SET DATA_TRANSFORMER_ENABLED = TRUE SCOPE=SPFILE;
ALTER TABLE orders_info ENABLE TRANSFORM;
ALTER TABLE orders_info ENABLE TRANSFORM BUILD AC COMPACT;
ALTER TABLE orders_info DISABLE TRANSFORM;
ALTER TABLE orders_info DISABLE COMPACT;
```

<span id="mcolttlclause" name="mcolttlclause" class="yaslink"></span>

##### 1.5.2. mcol\_ttl\_clause

This statement is used to modify the mutable lifecycle of LSC tables. For its meaning, refer to the corresponding statement description in [CREATE TABLE](CREATE TABLE).

This statement does not apply to ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment LSC tables

```sql
-- Modify the mutable lifecycle of finance_info from 1 month to 10 days
ALTER TABLE finance_info ALTER MCOL TTL '10' DAY;

-- Set the mutable lifecycle of the area table to 10 years during its creation
ALTER TABLE area ALTER MCOL TTL '10' YEAR(9);
```

<span id="mcolabilityclause" name="mcolabilityclause" class="yaslink"></span>

##### 1.5.3. mcol\_ability\_clause

This statement is used to enable or disable the overall functionality of mutable data (MCOL) of LSC tables, or the sorting functionality. For details on sorting, refer to the `table_sort_clause` statement description in [CREATE TABLE](CREATE TABLE).

Disabling MCOL functionality will cause data inserted or modified in the LSC table to immediately become non-mutable data upon commit. If the business does not have high transactional requirements, it is recommended to disable this module functionality.

If MCOL functionality is to be disabled for a specific LSC table, ensure that the table's background data transformation capability (ENABLE TRANSFORM) is already enabled.

***Example*** for LSC tables

```sql
ALTER TABLE orders_info ENABLE MCOL ORDER BY;
ALTER TABLE orders_info DISABLE MCOL ORDER BY;
ALTER TABLE orders_info ENABLE MCOL;

-- To disable MCOL for a certain table, ENABLE TRANSFORM must be executed first
ALTER TABLE orders_info ENABLE TRANSFORM;
ALTER TABLE orders_info DISABLE MCOL;
```

<span id="forcexfmrclause" name="forcexfmrclause" class="yaslink"></span>

##### 1.5.4. force\_xfmr\_clause

This statement is used for forced conversion of LSC tables, where the ASYNC flag indicates asynchronous conversion, and if omitted, synchronous conversion is assumed.

Forced conversion has the following three modes:

- STABLE: Forces the mutable data of the LSC table to convert to stable data and generates all AC data for this table.
- COMPACT: Forces the stable data of the LSC table to merge.
- CLEAN: Forces the deletion of all deletable data in the LSC (i.e., data that has met deferred deletion conditions after completion of transformation tasks).

***Example*** for LSC tables

```sql
ALTER TABLE sales_info ALTER SLICE ALL STABLE;
ALTER TABLE sales_info ALTER SLICE ALL COMPACT;
ALTER TABLE sales_info ALTER SLICE ALL CLEAN;
```

<span id="loggingclause" name="loggingclause" class="yaslink"></span>

#### 1.6. logging\_clause

This statement is used to modify the logging properties of a table, which specify how changes to the table are logged.

This statement is not applicable to ISC Distributed Cluster Deployment.

##### 1.6.1. LOGGING

This statement is used to set the table to logging properties, meaning all operations on this table are logged.

- If this statement is executed on a table that is already set to logging properties, it will return success directly.
- If executed on a table with nologging properties, the system will perform a full checkpoint, write data to disk, modify flushback, and finally change the table's logging properties.

##### 1.6.2. LOGGING ASYNC

This statement is used to convert the table to logging properties asynchronously. A new thread is started to complete the conversion of the table mode, allowing the main thread to continue working without blocking.

Note that when the client returns success, it does not guarantee that the conversion has been successful; it only confirms that the thread has successfully started. Subsequent conversions may still fail. The results of the conversion are recorded in the [runtime logs](../../All Manuals/数据库管理/运行监控/日志管理/运行日志管理).

While converting a table to logging properties asynchronously, the following constraints apply:

- No DML operations can be executed on the table being converted.
- No DDL operations can be executed on the table being converted, except for DROP.

##### 1.6.3. NOLOGGING

This statement is used to set the table to nologging properties. If executed on a table that is already set to nologging properties, it will return success directly. If it is executed on a table set to logging properties, it will change its properties to nologging. This property should only be enabled in data migration scenarios.

The execution of this statement comes with the following constraints:

- Temporary tables cannot be set to nologging properties.
- Tables with columns of UDT or built - in UDT (such as XMLTYPE, ST_GEOMETRY, BOX2D) cannot be set to nologging properties.
- In primary/standby environments, tables cannot be set to nologging properties.

The characteristics of nologging tables are as follows:

- DML operations on nologging tables only support data insertion and import, and only necessary redo and undo (such as segment-related redo) will be recorded during data insertion; other redo will be ignored.
- Concurrency operations cannot be performed on nologging tables.
- Rollback operations cannot be performed on nologging tables.
- Online index creation ([CREATE INDEX ONLINE](CREATE INDEX)) and online reindexing ([ALTER INDEX REBUILD ONLINE](ALTER INDEX)) cannot be executed on nologging tables.
- Tables marked as nologging will be marked as corrupted upon database restart, preventing any operations other than drop and truncate.
- If a transaction fails, all nologging tables that had insertion operations executed within that transaction will be marked as corrupted.
- Data loading using [bulkload mode](LOAD DATA) in nologging LSC tables is unaffected (performance remains unchanged and failed transactions will not be marked as corrupted).
- nologging TAC tables do not allow column modifications or deletions of primary key constraints.
- Truncating a nologging table will change its properties back to logging.
- In YAC/Distributed Cluster Deployment, if instance startups or joins occur, or YAC faults happen, all nologging tables will be marked as corrupted.
- In YAC/Distributed Cluster Deployment, setting table properties to nologging will bind the instance (the bound instance ID can be viewed via the GV$DICT_CACHE view with the nologging_instance_id field). The following operations can only be executed on the bound instance:
    - Inserting data into nologging tables.
    - Changing tables from nologging properties to logging.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Execute the following statement to enable logging
ALTER TABLE area LOGGING;

-- Attempting to set the table to nologging will throw an error in primary/standby environments
ALTER TABLE area NOLOGGING;
YAS-02328 table nologging is not allowed when standby exists
```

<span id="parallelclause" name="parallelclause" class="yaslink"></span>

#### 1.7. parallel\_clause

This statement is used to modify the parallel property of the table, which specifies the degree of parallelism when querying the table. The syntax is the same as the [parallel_clause](CREATE TABLE.html#parallelclause) in CREATE TABLE.

***Example***

```sql
-- Enable parallel and specify a parallel degree of 8
ALTER TABLE area PARALLEL 8;

-- Disable parallel
ALTER TABLE area NOPARALLEL;
```

<span id="readonlyclause" name="readonlyclause" class="yaslink"></span>

#### 1.8. readonly\_clause

This clause has no practical significance.

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

### 2. alter\_column\_clause

This statement specifies operations on column fields of the table.

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### 2.1. add\_column\_clause

This statement is used to add columns to the table. When adding multiple columns, they should be separated by `,` within `()`; the COLUMN keyword can be omitted.

<span id="columndefinition" name="columndefinition" class="yaslink"></span>

##### 2.1.1. column\_definition

A new column is added to the table, defining data types (DataType), default values (DEFAULT), inline constraints (inline_constraint), etc.

When adding a column defined with the NOT NULL constraint to a table that is not null, a default value must also be provided; otherwise, an error will be raised.

###### 2.1.1.1. DataType

This specifies the data type of the column field. Refer to YashanDB's [data type descriptions](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/00数据类型).

If the data type specified is of LOB/JSON type, its storage properties can be specified using the [lob_clauses](#lobclause) subclause.

YashanDB does not allow newly added columns to be defined as Nested Table UDT types.

###### 2.1.1.2. DEFAULT

This specifies a default value that complies with the data type of the column being added. The `default_expr` can be a literal, expression, or function.

If a default value is specified, the system will populate the default value for this column field in existing rows of the table.

- For newly added columns of type XMLTYPE in HEAP tables, the default value cannot be specified.

- For newly added LSC table columns, the default value cannot be specified if the column is of LOB type; for other types, the default value cannot be defined as a [Sequence pseudo-column](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Pseudo Columns).

***Example*** for Standalone Deployment LSC tables

```sql
-- Create an LSC table lsc_forb_def
CREATE TABLE lsc_forb_def(c1 INT,c2 INT);

-- Adding a lob column with a default value in an LSC table will raise an error
ALTER TABLE lsc_forb_def ADD(c3 CLOB DEFAULT 'default');
YAS-00004 feature "add lob column with default expr on LSC table" has not been implemented yet

-- Create sequence seq1
CREATE SEQUENCE seq1;

-- Adding a column in an LSC table with a default value as a Sequence pseudo-column will raise an error
ALTER TABLE lsc_forb_def ADD(c3 INT DEFAULT seq1.NEXTVAL);
YAS-00004 feature "add column with default sequence on LSC table" has not been implemented yet

ALTER TABLE lsc_forb_def ADD(c3 INT DEFAULT seq1.currval);
YAS-00004 feature "add column with default sequence on LSC table" has not been implemented yet
```

###### 2.1.1.3. identity\_clause

This clause sets the identity attributes of the specified column. For configuration syntax, refer to [identity_clause](#identityclause).

###### 2.1.1.4. inline\_constraint

This clause is used to define inline constraints when adding a column field. Refer to general SQL syntax [constraints](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint) for detailed descriptions.

***Example***

```sql
-- Add a non-null address field to the area table
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

###### 2.1.1.5. column\_encryption\_clause

This clause is used to define encryption attributes for the column being added, following the same rules as [CREATE TABLE](CREATE TABLE.html#columnencryptionclause).

***Example*** for Heap tables and LSC tables

```sql
-- Add an encrypted address column to the table
ALTER TABLE encrypt_col_employees ADD address VARCHAR(200) ENCRYPT USING 'AES128';
```

<span id="lobclause" name="lobclause" class="yaslink"></span>

##### 2.1.2. lob\_clause

This statement specifies the storage properties for the added LOB/JSON field, following the description of the lob_clause subclause in [CREATE TABLE](CREATE TABLE).

<span id="dropcolumnclause" name="dropcolumnclause" class="yaslink"></span>

#### 2.2. drop\_column\_clause

This statement is used to delete existing column fields or constraints in the table.

<span id="dropcolumn" name="dropcolumn" class="yaslink"></span>

##### 2.2.1. drop\_column

This statement deletes specified column fields, with multiple columns separated by `,`. The COLUMN keyword can be omitted.

The following rules apply:

- It is not allowed to delete all column fields from the table.
- If the column being deleted is defined as part of a primary key combination, the column cannot be removed; however, the entire primary key combination may be deleted.
- If the column being deleted is defined as part of an index object, deleting this column will also delete the corresponding index object.
- In ISC Distributed Cluster Deployment, it is not allowed to delete column fields that are partition key columns.
- In LSC tables, it is not allowed to delete column fields defined as sort keys.
- If the database is currently performing rollbacks, it is not allowed to delete columns stored using variable-length storage in TAC tables or LSC tables containing hot data.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create product_pri table
CREATE TABLE product_pri AS SELECT * FROM product;

-- Define composite primary key product_no+product_name
ALTER TABLE product_pri ADD PRIMARY KEY(product_no,product_name);

-- Create index
CREATE UNIQUE INDEX idx_product_pri_1 ON product_pri(cost,price);
   
-- Attempt to delete part of the primary key fields; this will raise an error
ALTER TABLE product_pri DROP COLUMN product_no;
YAS-02132 cannot drop column referenced in a multi-column constraint
   
-- Successfully delete all primary key fields
ALTER TABLE product_pri DROP COLUMN(product_no,product_name);

-- Successfully delete part of the index fields
ALTER TABLE product_pri DROP COLUMN cost;
   
-- Attempt to delete the last column in the table; this will raise an error
ALTER TABLE product_pri DROP COLUMN price;
YAS-02054 cannot drop all columns in a table
```

***Example*** for ISC Distributed Cluster Deployment TAC tables

```sql
-- Create area_part table
CREATE TABLE area_part AS SELECT * FROM area;

-- Attempt to delete the partition key column; this will raise an error
ALTER TABLE area_part DROP COLUMN area_no;
YAS-02142 cannot drop partitioning column
   
-- Successfully delete a non-partition key column
ALTER TABLE area_part DROP COLUMN area_name;
```

***Example*** for LSC tables

```sql
-- Create area_orderkey table, specifying order key as area_no
CREATE TABLE area_orderkey AS SELECT * FROM area ORDER BY area_no;

-- Attempt to delete the sort key column; this will raise an error
ALTER TABLE area_orderkey DROP COLUMN area_no;
YAS-03726 cannot drop order key column
```

<span id="renamecolumnclause" name="renamecolumnclause" class="yaslink"></span>

#### 2.3. rename\_column\_clause

This statement is used to rename a column field. The new specified name cannot be empty and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

***Example*** for Heap tables and TAC tables

```sql
ALTER TABLE branches RENAME branch_no TO branchno;
ALTER TABLE branches RENAME branchno TO branch_no;
```

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### 2.4. modify\_column\_clause

This statement is used to modify the data type, default value, constraints, and other properties of a column field. When modifying multiple column fields, they must be separated within `()` by `,`.

YashanDB supports modifying multiple properties of a column field at the same time, but the order must adhere to the rules of [CREATE TABLE](CREATE TABLE); otherwise, the system will prompt an error. When modifying multiple constraints at the same time, there are no order requirements for those constraints.

Modification of the properties of a virtual column is not allowed.

##### 2.4.1. dataType

This modifies the data type of the column field to one recognized by YashanDB [data types](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/00数据类型).

LSC tables do not allow the modification of column field data types. The rules for modifying data types of column fields in HEAP and TAC tables are as follows:

- **Columns with foreign key constraints**: Data types of the corresponding columns in both the child and parent tables may not be modified.
- **Data columns on which virtual columns depend**: Modifying their data types is not allowed; attempting to do so will return an error message.
- **Columns with indexes**:
  
    - If the table is empty (i.e., contains no data), it is allowed to modify its data type to any type except LOB, JSON, or UDT, but the modified result must still comply with [the rules for index columns](./CREATE INDEX.html#indexexpr).

    - For non-empty tables, modifying data types is not allowed.

- **Columns encoded using dictionary (only for TAC tables)**: Only character types are allowed; the modification is still subject to character type modification rules.
- **Variable-length character type columns (only for TAC tables)**: Modification of data types is not allowed when the database is in the process of rollbacks.
- **Columns that are partition keys or those which are part of function indexes or AC**: Modification of data types is not allowed.
- **Columns with original data types of LOB/JSON/UDT**: Modification of data types is not allowed.
- **Other empty columns with original data types of character types**: It is allowed to modify them to other data types other than LOB/JSON/UDT.
- **Other non-empty columns with original data types of character types**: Modifications to different character types are allowed, but still must follow these rules:

    - CROSS modification between CHAR/VARCHAR and NCHAR/NVARCHAR is not allowed.

    - When modifying the length property from larger to smaller, it must be ensured that all existing data in this column does not exceed the target data type's limit; otherwise, the modification will not succeed and an error will be prompted.

    - HEAP tables do not allow modification of data types across storage modes. For example, in HEAP tables, it is not allowed to modify the column data type from VARCHAR(3200) (with standard string storage) to VARCHAR(8004) (which will automatically convert to LOB storage). TAC tables, on the other hand, allow such operations. For an introduction to storage methods, refer to [character types](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/字符型).

    - HEAP tables may modify the length attribute of columns stored as LOB type. For instance, a HEAP table can change a column's data type from VARCHAR(32000) (which will automatically convert to LOB storage) to VARCHAR(1000), but after the modification, the column's storage mode will still remain LOB.

- **Other empty columns with original data types of other data types**: It is allowed to change them to data types that are not LOB/JSON/UDT.
  
- **Other non-empty columns with original data types of other data types**:

    - Cross-category modification of data types is not allowed, for instance, it is not permitted to change a numeric type to character type.

    - Within the same data category, modifications are only allowed from small to large values, precision, or length attributes. For instance, INT cannot be modified to a NUMBER specified with precision, nor can DATE be modified to TIME.

##### 2.4.2. DEFAULT

This modifies or deletes the default value of the column field. The `default_expr` can be a literal, expression, or function.

***Example*** for Heap tables and TAC tables

```sql
-- Create a sample table and insert sample data
DROP TABLE IF EXISTS area;


CREATE TABLE area
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) NOT NULL,
 owner VARCHAR2(20));


INSERT INTO area VALUES ('01','EastChina','Shanghai','');

INSERT INTO area VALUES ('02','WestChina','Chengdu','');

INSERT INTO area VALUES ('03','SouthChina','Guangzhou','');

INSERT INTO area VALUES ('04','NorthChina','Beijing','');

INSERT INTO area VALUES ('05','CentralChina','Wuhan','');


SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan


-- Modify the default value of column owner to 'zq', insert sample data and check the default value has taken effect
ALTER TABLE area MODIFY owner DEFAULT 'zq';

INSERT INTO area VALUES ('06','CentralChina','Changsha','cc');

INSERT INTO area VALUES ('07','CentralChina','Hengyang','');

INSERT INTO area VALUES ('08','CentralChina','Ezhou',DEFAULT);

SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
06      CentralChina                                                          Changsha              cc
07      CentralChina                                                          Hengyang
08      CentralChina                                                          Ezhou                 zq


-- Remove the default value configuration from the owner column, insert sample data and check the default value has been deleted
ALTER TABLE area MODIFY owner DEFAULT null;


INSERT INTO area VALUES ('10','CentralChina','Enshi','');


SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ                   OWNER
------- ------------------------------------------------------------- --------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
06      CentralChina                                                          Changsha              cc
07      CentralChina                                                          Hengyang
08      CentralChina                                                          Ezhou                 zq
10      CentralChina                                                          Enshi


-- Delete the table and clean up test data

DROP TABLE IF EXISTS area;

```

##### 2.4.3. identity\_clause

This modifies the identity attributes of a specified column; for configuration syntax, refer to [identity_clause](#identityclause).

##### 2.4.4. inline\_constraint

This modifies the constraints of a column field, following the syntax rules defined in general [constraints](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint).

###### 2.4.4.1. modify NULL/NOT NULL

Modifying NOT NULL constraints:

- Cannot execute modify NULL on column fields that are not defined with NOT NULL.
- Cannot execute modify NOT NULL on column fields that are already defined with NOT NULL.
- Cannot execute modify NOT NULL on column fields that already contain NULL data.
- Cannot execute modify NOT NULL on column fields defined as ON DELETE SET NULL or ON UPDATE SET NULL foreign keys.

###### 2.4.4.2. modify UNIQUE 

Modifying UNIQUE constraints:

- Cannot execute modify UNIQUE on column fields that already define UNIQUE constraints.
- Cannot execute modify UNIQUE on column fields that already contain duplicate data.
- Cannot execute modify UNIQUE on multiple column fields simultaneously.

###### 2.4.4.3. modify PRIMARY KEY

Modifying primary key constraints:

- Cannot execute modify PRIMARY KEY on any column fields of a table that already has a primary key.
- Cannot execute modify PRIMARY KEY on column fields that are defined with UNIQUE constraints.
- Cannot execute modify PRIMARY KEY on column fields that do not meet the requirements for creating primary keys. For the requirements for creating primary keys, refer to [constraints](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint).

###### 2.4.4.4. modify CHECK

Modifying CHECK constraints:

- If the new CHECK conflicts with existing other CHECKs in the table, modification cannot be executed.
- If existing data in the table does not meet the CHECK condition, modification cannot be executed.

###### 2.4.4.5. modify FOREIGN KEY

Modifying foreign key constraints:

- Cannot execute modify FOREIGN KEY on column fields that do not meet the requirements for creating foreign keys; for the requirements for creating foreign keys, refer to [constraints](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint).

***Example*** for Heap tables and TAC tables

```sql
-- Modify the non-null column field data type of branch_name in the branches non-null table; only character types are permitted
ALTER TABLE branches MODIFY branch_name CHAR(400);

-- Modifying both the branch_no and branch_name fields in the branches table to UNIQUE is invalid; error will be raised
ALTER TABLE branches MODIFY (branch_no,branch_name) UNIQUE;
YAS-04297 invalid ALTER TABLE option

-- Modify the default value of the address column field in the branches table
ALTER TABLE branches MODIFY address DEFAULT 'no address';

-- Modify the area_name column field in the area table to be non-null
ALTER TABLE area MODIFY area_name NOT NULL;
```

<span id="altertablepartition" name="altertablepartition" class="yaslink"></span>

### 3. alter\_table\_partition

This statement is used to specify operations on the partitions of a table.

If a partition index has been established on the table, when adding or deleting partitions, the system will also simultaneously add or delete the indexing partitions.

In ISC Distributed Cluster Deployment, this statement is not allowed for first-level partitions.

<span id="addtablepartition" name="addtablepartition" class="yaslink"></span>

#### 3.1. add\_table\_partition

This statement is used to add a new partition to the table.

Different types of tables can add different types of partitions:

- HEAP tables support adding range, list, and hash type partitions.
- TAC tables support adding range, list, and hash type partitions.
- LSC tables support adding range and list type partitions.

<span id="addrangepartitionclause" name="addrangepartitionclause" class="yaslink"></span>

##### 3.1.1. add\_range\_partition\_clause

Add a range type partition, which can only be established above the current maximum partition boundary. If the maximum partition boundary of the table is set to MAXVALUE, adding a partition is not allowed.

For range partition tables with defined INTERVAL, partitions are automatically maintained by the system and cannot be added using this statement.

<span id="tablepartitiondescription" name="tablepartitiondescription" class="yaslink"></span>

###### 3.1.1.1. table\_partition\_description

You can specify the tablespace for the newly added partition (defaulting to the tablespace of the table), as well as attributes like PCTFREE, PCTUSED, INITRANS, MAXTRANS.

You can specify the range, list, or hash subpartitions for the newly added partition, and the type of subpartition should match the table's definition.

If the table was not specified as a composite partition table during creation, you cannot specify subpartitions for the newly added partition using this statement.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER TABLE sales_info_range ADD PARTITION p_sales_info_range_4 VALUES LESS THAN('2038');

-- Create a range-list composite partition table range_list_table
CREATE TABLE range_list_table(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY LIST(b)
(PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1 VALUES(10), SUBPARTITION sp2 VALUES(20)),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3 VALUES(10), SUBPARTITION sp4 VALUES(20)));

-- Add a range partition to it and specify its list subpartition 
ALTER TABLE range_list_table ADD PARTITION p3 VALUES LESS THAN(3)(SUBPARTITION sp5 VALUES(10),SUBPARTITION sp6 VALUES(20));
```

<span id="addlistpartitionclause" name="addlistpartitionclause" class="yaslink"></span>

##### 3.1.2. add\_list\_partition\_clause

Add a list type partition. If the list values have already been defined with DEFAULT in the table, adding a partition is not allowed.

The partition list values can be specified as a general expression ([expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)).

###### 3.1.2.1. table\_partition\_description

You can specify the tablespace for the newly added partition (defaulting to the tablespace of the table), as well as attributes like PCTFREE, PCTUSED, INITRANS, MAXTRANS.

You can specify the range, list, or hash subpartitions for the newly added partition, and the type of subpartition should match the table's definition.

If the table was not specified as a composite partition table during creation, you cannot specify subpartitions for the newly added partition using this statement.

When the partition column count is one and the newly added partition contains multiple values, each value needs to be wrapped in parentheses; otherwise, an error will occur.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER TABLE sales_info_list ADD PARTITION p_sales_info_list_3 VALUES ('2022');
ALTER TABLE sales_info_list ADD PARTITION p_sales_info_list_4 VALUES (TO_CHAR(2023));

-- Create a list-list composite table list_list_table
CREATE TABLE list_list_table(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY LIST(b)
(PARTITION p1 VALUES(1) (SUBPARTITION sp1 VALUES(1), SUBPARTITION sp2 VALUES(2)),
PARTITION p2 VALUES(2) (SUBPARTITION sp3 VALUES(1), SUBPARTITION sp4 VALUES(2)));

-- Add a list partition to it and specify its list subpartition
ALTER TABLE list_list_table ADD PARTITION p3 VALUES (3)(SUBPARTITION sp5 VALUES(1), SUBPARTITION sp6 VALUES(2));
```

<span id="addhashpartitionclause" name="addhashpartitionclause" class="yaslink"></span>

##### 3.1.3. add\_hash\_partition\_clause

Add a hash type partition. This statement is only applicable to HEAP and TAC tables.

The PCTFREE, PCTUSED, INITRANS, MAXTRANS, and other attributes of the newly added partition are inherited from the table and cannot be specified.

You can specify range, list, or hash subpartitions for the newly added partition, and the type of subpartition should match the table's definition.

If the table was not specified as a composite partition table during creation, you cannot specify subpartitions for the newly added partition using this statement.

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

###### 3.1.3.1. partition\_storage\_clause

You can specify the tablespace for the newly added partition (defaulting to the tablespace of the table).

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
ALTER TABLE sales_info_hash ADD PARTITION p_sales_info_hash_3;
ALTER TABLE sales_info_hash ADD PARTITION p_sales_info_hash_4;

-- Create a hash-list composite partition table hash_list_table
CREATE TABLE hash_list_table(c1 INT, c2 VARCHAR(10))
PARTITION BY HASH(c1)
SUBPARTITION BY LIST(c2)
(
PARTITION p1(SUBPARTITION sp1 VALUES('a')), 
PARTITION p2 (SUBPARTITION sp3 VALUES('d'), SUBPARTITION sp4 VALUES(DEFAULT))
);
-- Add a hash partition to it and specify its list subpartition 
ALTER TABLE hash_list_table ADD PARTITION p3(SUBPARTITION sp5 VALUES ('f'));
```

<span id="droptablepartition" name="droptablepartition" class="yaslink"></span>

#### 3.2. drop\_table\_partition

This statement is used to delete the partition of a table, deleting all data within the partition. Specify multiple partitions with commas.

When a partition specified for deletion contains data, the global indexes on the table will become invalid. In ISC Distributed Cluster Deployment mode, regardless of whether the specified partition contains data, the global indexes on the table will become invalid.

The following constraints apply to this statement:

- When deleting partitions, it is not permitted to delete all partitions on the table.
- Hash type partitions cannot be deleted.
- When a partition on the table is deleted, the corresponding index partition is also deleted.
- For range type partitions, after deleting a partition, the boundary of that partition will merge with the neighboring larger partition. Hence, any new table data that falls within the boundary range of the deleted partition will be inserted into the neighboring partition. If there is no neighboring larger partition, the data insertion will not be successful.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- sales_info_range is a range partition table
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON  
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0201   11001              30         500 0201010011 
2000  12    0102   11001              20         300            
2015  11    0101   11001              20         300            
2015  03    0102   11001              20         300            
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600       
   
-- Deleting the partition and data
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

Specifies how to handle global indexes (Global Index) when a partition is deleted, defaulting to INVALIDATE.

*   INVALIDATE GLOBAL INDEXES: All global indexes will be invalidated and become unavailable.
*   UPDATE GLOBAL INDEXES: The global indexes remain valid and available.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER TABLE sales_info_list DROP PARTITION p_sales_info_list_4 UPDATE GLOBAL INDEXES;
```

<span id="droptablesubpartition" name="droptablesubpartition" class="yaslink"></span>

#### 3.3. drop\_table\_subpartition

This statement is used to delete the subpartitions of a table, deleting all data within the subpartitions. Specify multiple subpartitions with commas.

The following constraints apply to this statement:

- When deleting subpartitions, it is not allowed to delete all subpartitions under a partition and it is also not permitted to delete subpartitions across partitions.
- Hash type subpartitions cannot be deleted.
- When a subpartition of the table is deleted, the corresponding index subpartition will also be deleted.

***Example***

```sql
-- Query the names of subpartitions for the sales_info table
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
-- The following output is an example for a standalone
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

-- Delete one of the subpartitions
ALTER TABLE sales_info DROP SUBPARTITION P_SALES_INFO_1_SP_SALES_INFO_1;
```

##### 3.3.1. update\_index\_clause

Described in the same way as the drop_table_partition statement.

<span id="truncatetablepartition" name="truncatetablepartition" class="yaslink"></span>

#### 3.4. truncate\_table\_partition

This statement is used to delete all data within specified partitions. To specify multiple partitions, separate them with commas.

This statement also deletes the corresponding local index data (Local Index) for the partition.

If the partition specified contains data, the global indexes on the table will become invalid. In ISC Distributed Cluster Deployment mode, the global indexes will become invalid regardless of whether the specified partitions contain data.

<span id="truncatepartclause" name="truncatepartclause" class="yaslink"></span>

##### 3.4.1. truncate\_part\_clause

This statement specifies the method of deleting data from partitions.

###### 3.4.1.1. DROP|REUSE STORAGE

Specifies whether to release or retain the storage space after deleting partition data.

###### 3.4.1.2. CASCADE

If a partition table defined as a parent table has foreign key constraints from child tables, and the data corresponding to the deleted partition exists in the child table's corresponding column, specifying CASCADE will simultaneously delete the corresponding rows in the child tables. This statement applies only to HEAP tables.

###### 3.4.1.3. PURGE

When the recycle bin is enabled, deleted data will by default enter the recycle bin; specifying this keyword means the data will be permanently deleted without entering the recycle bin. This statement applies only to HEAP tables.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Query the names of partitions for the sales_info table
SELECT partition_name
FROM DBA_TAB_PARTITIONS
WHERE table_name='SALES_INFO';
-- The following output is an example for a standalone
PARTITION_NAME
----------------------------------------------------------------
P_SALES_INFO_1
P_SALES_INFO_2
P_SALES_INFO_3

ALTER TABLE sales_info TRUNCATE PARTITION P_SALES_INFO_1 DROP STORAGE;
```

<span id="truncatetablesubpartition" name="truncatetablesubpartition" class="yaslink"></span>

#### 3.5. truncate\_table\_subpartition

This statement is used to delete all data from specified subpartitions. Specify multiple subpartitions with commas.

This statement also deletes the local index data (Local Index) corresponding to the subpartitions.

##### 3.5.1. truncate\_part\_clause

Described in the same way as the truncate_table_partition statement.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER TABLE sales_info TRUNCATE SUBPARTITION P_SALES_INFO_3_SP_SALES_INFO_1 DROP STORAGE;
```

<span id="setpartitionclause" name="setpartitionclause" class="yaslink"></span>

#### 3.6. set\_partition\_clause

This statement only applies to HEAP tables and only for range partition tables. It is used to specify conversions between INTERVAL and non-INTERVAL partition types. Converting the partition type does not affect the original data in the table; new data will be stored according to the features of the converted partition type.

In ISC Distributed Cluster Deployment, users cannot execute this operation.

##### 3.6.1. transfer range to range-interval

Converts a RANGE partition table to an INTERVAL partition table, requiring the data type of the partition key to be a number or date, and the syntax is as follows:

```sql
ALTER TABLE table_name SET INTERVAL(expr);
```

The requirements and rules for establishing INTERVAL partitions are described in the [CREATE TABLE](CREATE TABLE) interval_clause, and failure to meet those requirements will result in conversion failure.

***Example*** for Heap tables

```sql
-- Create sample table sales_info_range1, which differs from the sales_info_range table only in that the data type of the year partition key is int
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
-- Convert the sales_info_range1 table's partition type to INTERVAL type
ALTER TABLE sales_info_range1 SET INTERVAL(2);

-- For data exceeding the existing partition boundaries, the system will create a new partition of type INTERVAL
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

Modifies the attributes of an INTERVAL partition.

###### 3.6.2.1. Modify INTERVAL value

Adjust the INTERVAL to a new value. Data inserted afterward will create partitions based on the new value.

The syntax for this functionality is:

```sql
ALTER TABLE table_name SET INTERVAL(expr);
```

Relevant requirements and rules are consistent with the above transfer range to range-interval.

###### 3.6.2.2. Modify partition tablespace

Specify the tablespace for INTERVAL partitions, separating multiple tablespaces with commas. All newly created partitions will then cycle through these tablespaces.

The syntax for this functionality is:

```sql
ALTER TABLE table_name SET STORE IN(tablespace_name, ...);
```

This operation can only be performed on range partition tables of INTERVAL type; if the operation is performed on non-INTERVAL range partition tables, an error will be reported by the system.

***Example*** for Heap tables

```sql
-- Create two new tablespaces for sales_info_range1 table
CREATE TABLESPACE sales_tb1;
CREATE TABLESPACE sales_tb2;

-- Modify the INTERVAL and tablespace attributes of INTERVAL partition table in the previous example
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

This statement converts an INTERVAL range partition to a non-INTERVAL range partition, and the syntax is as follows:

```sql
ALTER TABLE table_name SET INTERVAL();
```

***Example*** for Heap tables

```sql
-- Modifying the previous INTERVAL partition table back to a non-INTERVAL range partition table
ALTER TABLE sales_info_range1 SET INTERVAL();
```

<span id="modifypartitionclause" name="modifypartitionclause" class="yaslink"></span>

#### 3.7. modify\_partition\_clause

This statement is used for operations on table partitions. It includes shrink operations and operations to add second-level partitions for first-level partitions, while only shrink operations can be performed on second-level partitions. 

##### 3.7.1. shrink\_space\_clause

The shrink operation is consistent with table shrink. Please refer to [shrink_space_clause](#shrinkspaceclause). 

***Example*** for Heap tables

```sql
ALTER TABLE orders_info ENABLE ROW MOVEMENT;
ALTER TABLE sales_info ENABLE ROW MOVEMENT;

ALTER TABLE orders_info MODIFY PARTITION p_orders_info_1 SHRINK SPACE COMPACT CASCADE;
ALTER TABLE sales_info MODIFY SUBPARTITION P_SALES_INFO_3_SP_SALES_INFO_1 SHRINK SPACE COMPACT CASCADE;
```

<span id="addsubpartitionclause" name="addsubpartitionclause" class="yaslink"></span>

##### 3.7.2. add\_subpartition\_clause

This statement is used to add second-level partitions to specified first-level partitions within composite partition tables. 

The range_subpartition_desc, list_subpartition_desc, and individual_hash_subparts descriptions can be referenced in [CREATE TABLE](CREATE TABLE).

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
CREATE TABLE composite_table(c1 INT, c2 INT)
PARTITION BY RANGE(c1)
SUBPARTITION BY HASH(c2)
(
PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1, SUBPARTITION sp2),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3, SUBPARTITION sp4)
);

ALTER TABLE composite_table MODIFY PARTITION p1 ADD SUBPARTITION p1_subp1;

-- Since the table was not specified as a composite partition table when created, executing this statement will return an error
CREATE TABLE partition_table(c1 INT,c2 INT)
PARTITION BY RANGE(c1)
INTERVAL (10)
(PARTITION par1 VALUES LESS THAN(100));

ALTER TABLE partition_table MODIFY PARTITION par1 ADD SUBPARTITION par1_subp1;
YAS-02374 table is not partitioned by composite partition method
```

<span id="splittablepartition" name="splittablepartition" class="yaslink"></span>

#### 3.8. split\_table\_partition

This statement can re-partition a range or list partition into multiple partitions. When a partition becomes too large and impacts query, backup, and other performance, consider using SPLIT PARTITION.

This statement can only be executed on HEAP tables and standalone TAC tables.

The statement must adhere to the following restrictions:

- SPLIT PARTITION cannot be executed on composite partition tables.
- By default, global indexes will become invalid after SPLIT PARTITION. Please either [rebuild (rebuild_clause)](ALTER INDEX) the global index after the SPLIT, or specify UPDATE GLOBAL INDEXES.
- If UPDATE INDEXES is specified, local indexes and global indexes will not become invalid after SPLIT PARTITION.
- For tables without specified partition names, partition names, index partition names, and Lob partition names will be automatically generated in the format SYS_Pn.

##### 3.8.1. SPLIT AT (literal) INTO

This statement divides a range partition into two range partitions based on the specified literal, applicable only to range partitions.

###### 3.8.1.1. literal

This is the split value, which must be within the range of the partition being split; otherwise, an error will be returned.

###### 3.8.1.2. range\_partition\_desc

This statement is used to specify the names of the two range partitions after splitting. One partition name can retain the original partition name, while the other partition name must not duplicate existing partition names.

The lower and upper bounds of the new range partitions are `[original lower limit, literal]` and `[literal, original upper limit]`, respectively.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- split range partition 
DROP TABLE IF EXISTS split_range_part;
CREATE TABLE split_range_part(c1 INT)
PARTITION BY RANGE(c1)
(PARTITION p1 VALUES LESS than(100),
PARTITION p2 VALUES LESS than(200),
PARTITION p3 VALUES LESS than(300),
PARTITION p4 VALUES LESS than(MAXVALUE));
-- Split p4 into two partitions p4 and p5, with the boundary value of new p4 being 350 and p5's boundary value being maxvalue
ALTER TABLE split_range_part split PARTITION p4 at(350) INTO (PARTITION p4, PARTITION p5);
```

##### 3.8.2. SPLIT VALUES (list\_values) INTO

This statement splits a list partition into two list partitions based on specified list values, applicable only to list partitions.

###### 3.8.2.1. list\_values

This is the list of values to be split. The values must be included in the list partition being split; otherwise, an error will be returned.

###### 3.8.2.2. list\_partition\_desc

This statement is used to specify the names of the two list partitions after splitting. One partition name can retain the original partition name, while the other partition name must not duplicate existing partition names.

The first resulting list partition contains all values from list_values, and the remaining values are stored in the second list partition.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- split list partition 
DROP TABLE IF EXISTS split_list_part;
CREATE TABLE split_list_part(c1 INT)
PARTITION BY LIST(c1)
(PARTITION p1 VALUES (100, 150, 170),
PARTITION p2 VALUES (200, 250, 280),
PARTITION p3 VALUES (300, 400),
PARTITION p4 VALUES (DEFAULT));
-- Split p4 into partitions p4 and p5, with the new p4 partition only containing value 350, and p5 partition containing all values from default except 350
ALTER TABLE split_list_part split PARTITION p4 VALUES(350) INTO (PARTITION p4, PARTITION p5);
```

##### 3.8.3. SPLIT INTO (range/list\_partition\_desc) 

This statement can split a range or list partition into multiple partitions based on specified content, suitable for range partitions and list partitions.

###### 3.8.3.1. range\_partition\_desc|list\_partition\_desc

This statement is used to specify the detailed information of the partitions after the split, including partition names, types, and partition values. The new partition types must match the original partition type, and multiple partitions are separated by `,`.

One partition name after splitting can retain the original partition name, while the other partition names must not duplicate existing partition names.

###### 3.8.3.2. Partition partname table\_partition\_description

This statement specifies the name and information of the partition that contains all remaining values. The name must not duplicate existing partition names.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- split list partition 
DROP TABLE IF EXISTS split_list_part;
CREATE TABLE split_list_part(c1 INT)
PARTITION BY LIST(c1)
(PARTITION p1 VALUES (100, 150, 170),
PARTITION p2 VALUES (200, 250, 280),
PARTITION p3 VALUES (300, 400),
PARTITION p4 VALUES (DEFAULT));
-- Using split into syntax
ALTER TABLE split_list_part split PARTITION p4 INTO (PARTITION p4 VALUES(350), PARTITION p5);

-- split range partition 
DROP TABLE IF EXISTS split_range_part;
CREATE TABLE split_range_part(c1 INT)
PARTITION BY RANGE(c1)
(PARTITION p1 VALUES LESS than(100),
PARTITION p2 VALUES LESS than(200),
PARTITION p3 VALUES LESS than(300),
PARTITION p4 VALUES LESS than(MAXVALUE));
-- Using split into syntax
ALTER TABLE split_range_part split PARTITION p4 INTO (PARTITION p4 VALUES LESS than(350), PARTITION p5);
```

<span id="mergetablepartitions" name="mergetablepartitions" class="yaslink"></span>

#### 3.9. merge\_table\_partitions

This statement is used to merge multiple top-level partitions in a partitioned table into one partition.

This statement can only be executed on HEAP tables, and hash partitions cannot be merged.

The statement must adhere to the following restrictions:

- After merging range partitions, the upper boundary value of the resulting partition will inherit the largest upper boundary value among all original partitions, while the lower boundary value will inherit the smallest lower boundary value.
- After merging list partitions, the resulting partition's value list will be the union of all original partitions' value lists. If there is a DEFAULT list partition among the original partitions, the resulting partition will be the DEFAULT list partition.
- When merging composite partitions, it is not allowed to specify the secondary partition attributes for the resulting partition. The system automatically generates the secondary partitions based on the secondary partition template of the composite partition table; if there is no secondary partition template, the system automatically creates a MAXVALUE secondary partition for the new top-level range partition and a DEFAULT secondary partition for the new top-level list partition.

<span id="partitionorkeyvalue" name="partitionorkeyvalue" class="yaslink"></span>

##### 3.9.1. partition\_or\_key\_value

Used to specify the partitions to be merged.

For merging range partitions, a contiguous set of partitions (i.e., adjacent partitions) must be specified in increasing order by partition boundary.

##### 3.9.2. table\_partition\_description

We can specify the tablespace (defaulting to the tablespace of the table) and attributes like PCTFREE/PCTUSED/INITRANS/MAXTRANS for the new merged partition if needed; these can be omitted.

##### 3.9.3. UPDATE [GLOBAL] INDEXES

It can be specified whether to update the indexes when merging partitions. Omission means that indexes are not updated during the merge.

- Specifying UPDATE INDEXES means that MERGE PARTITION will update local indexes, and local indexes will remain valid after partition merging, and global indexes will not become invalid.
- If there is data in the original partitions, the global indexes will become invalid after MERGE PARTITION, and you will need to manually [rebuild (rebuild_clause)](ALTER INDEX.html#rebuildclause) the global indexes or specify UPDATE [GLOBAL] INDEXES to prevent the indexes from being invalid. If the original partitions do not contain data, MERGE PARTITION will not change the state of the global indexes.

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS students;
CREATE TABLE students(id INT,name VARCHAR(10)) PARTITION BY RANGE(id)(
    PARTITION p1 VALUES LESS than(10) TABLESPACE users,
    PARTITION p2 VALUES LESS than(20) TABLESPACE users,
    PARTITION p3 VALUES LESS than(30));
-- Merge p2 and p3 into p3; the partition boundary value of p3 remains unchanged
ALTER TABLE students MERGE partitions p2,p3 INTO PARTITION p3;

DROP TABLE IF EXISTS students;
CREATE TABLE students(id INT,name VARCHAR(10)) PARTITION BY RANGE(id)(
    PARTITION p1 VALUES LESS than(10) TABLESPACE users,
    PARTITION p2 VALUES LESS than(20) TABLESPACE users,
    PARTITION p3 VALUES LESS than(30));
-- Merge p2 and p3 into p4; the partition boundary value of p4 remains the same as the original p3
ALTER TABLE students MERGE partitions p2,p3 INTO PARTITION p4;

DROP TABLE IF EXISTS courses;
CREATE TABLE courses(id INT,name VARCHAR(10)) PARTITION BY LIST(id)(
    PARTITION p1 VALUES(1,2),
    PARTITION p2 VALUES(3,4),
    PARTITION p3 VALUES(5,6),
    PARTITION p4 VALUES(7,8)
);
-- Merge p3, p1, and p2 into p1; list values of p1 will be the union of original p3, p1, and p2, i.e., (1,2,3,4,5,6)
ALTER TABLE courses MERGE partitions p3,p1,p2 INTO PARTITION p1;
```

<span id="mergetablesubpartitions" name="mergetablesubpartitions" class="yaslink"></span>

#### 3.10. merge\_table\_subpartitions

This statement is used to merge multiple secondary partitions under the same top-level partition in a composite partition table into one secondary partition.

The usage rules are the same as for [merge_table_partitions](#mergetablepartitions).

<span id="subpartitionorkeyvalue" name="subpartitionorkeyvalue" class="yaslink"></span>

##### 3.10.1. subpartition\_or\_key\_value

Used to specify the secondary partitions to be merged.

For merging range partitions, a contiguous set of partitions (i.e., adjacent partitions) must be specified in increasing order by partition boundary.

***Example*** for Heap tables

```sql
CREATE TABLE merge_rr_composite ( col1 INT,col2 SMALLINT,col3 INT)
PARTITION BY RANGE(col1) SUBPARTITION BY RANGE(col3)
(PARTITION p1 VALUES LESS than (301)(SUBPARTITION sp11 VALUES LESS than (4000) , SUBPARTITION sp12 VALUES LESS than (8000) ,SUBPARTITION sp13 VALUES LESS than (12001)),
 PARTITION p2 VALUES LESS than (601)(SUBPARTITION sp21 VALUES LESS than (4000) , SUBPARTITION sp22 VALUES LESS than (8000) ,SUBPARTITION sp23 VALUES LESS than (12001)),
 PARTITION p3 VALUES LESS than (801)(SUBPARTITION sp31 VALUES LESS than (4000) , SUBPARTITION sp32 VALUES LESS than (8000) ,SUBPARTITION sp33 VALUES LESS than (12001))
);
-- Merge sp11 and sp12 into sp12
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
-- Merge sp4 and sp5 into sp10
ALTER TABLE merge_rl_composite MERGE subpartitions sp4, sp5 INTO SUBPARTITION sp10;
```

<span id="exchangeclause" name="exchangeclause" class="yaslink"></span>

#### 3.11. exchange\_partition\_clause

This statement is used to exchange data between a partition of a partitioned table and a non-partitioned table. CASCADE is used only for syntax compatibility and has no practical meaning.

This statement must follow these restrictions:

- Applicable only to HEAP tables, and the target table cannot be a partitioned table.

- The user must have read and write permissions on both the target partitioned table and the target regular table.

- The column definitions and constraints of the partition and target table must be identical.

- Columns of NESTED TABLE type are not allowed.

- If indexes are to be exchanged along with data (specify INCLUDING INDEXES), the local indexes on the partition must exactly match the indexes on the target table, both in one-to-one correspondence and definition.

##### 3.11.1. partition\_key\_value

Specifies the partition or subpartition of data to be exchanged.

##### 3.11.2. [schema.]table\_name

Specifies the target table for data exchange, which cannot be a partitioned table.

##### 3.11.3. INCLUDING|EXCLUDING INDEXES

Specifies whether to exchange indexes along with data during the exchange operation. This is optional and defaults to exclusion if omitted.

- INCLUDING INDEXES: Performs additional checks to verify that local indexes of the partitioned table match those of the regular table. If validation passes, the exchange is executed, including data exchange for both partitioned and non-partitioned indexes. If validation fails, an error is returned.

- EXCLUDING INDEXES: Skips index validation. After exchange, local indexes for the partition and regular table indexes become UNUSABLE.

##### 3.11.4. WITH|WITHOUT VALIDATION

Specifies whether to validate if incoming data meets partition definition requirements. This is optional and defaults to validation if omitted.

- WITH VALIDATION: Validates if incoming data meets partition definition requirements. If not, an error is returned.

- WITHOUT VALIDATION: Skips data validation, which may allow invalid data to enter the partition.

##### 3.11.5. UPDATE [GLOBAL] INDEXES

Specifies whether to update global indexes during data exchange. Both UPDATE INDEXES and UPDATE GLOBAL INDEXES indicate global index updates. This is optional and defaults to no update if omitted.

- Update: Deletes outgoing data from global indexes and inserts incoming data into global indexes.

- No update: Marks global indexes of the partitioned table as invalid.

***Example*** for Heap tables

```sql
-- Create partitioned table and indexes, then insert data
CREATE TABLE ex_sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY RANGE(year)
(PARTITION p_ex_sales_info_1 VALUES LESS THAN('2011'),
 PARTITION p_ex_sales_info_2 VALUES LESS THAN('2021'),
 PARTITION p_ex_sales_info_3 VALUES LESS THAN('2031'));

CREATE INDEX idx_ex_sales_info ON ex_sales_info (year,month,branch,product) LOCAL
(PARTITION p_ex_sales_info_1,
PARTITION p_ex_sales_info_2,
PARTITION p_ex_sales_info_3);

INSERT INTO ex_sales_info VALUES ('2001','01','0201','11001',30,500,'0201010011');
INSERT INTO ex_sales_info VALUES ('2015','11','0101','11001',20,300,'');
INSERT INTO ex_sales_info VALUES ('2021','10','0402','11001',20,300,'');
INSERT INTO ex_sales_info VALUES ('2000','12','0102','11001',20,300,'');
INSERT INTO ex_sales_info VALUES ('2015','03','0102','11001',20,300,'');
INSERT INTO ex_sales_info VALUES ('2021','05','0101','11001',40,600,'');

-- View data in partition p_ex_sales_info_1
SELECT year,month,branch,product,quantity,amount,salsperson FROM ex_sales_info PARTITION(p_ex_sales_info_1);

YEAR      MONTH     BRANCH    PRODUCT              QUANTITY      AMOUNT SALSPERSON
--------- --------- --------- ----------------- ----------- ----------- -------------------------
2001      01        0201      11001                      30         500 0201010011
2000      12        0102      11001                      20         300

-- Create target table (must not be a partitioned table)
CREATE TABLE p_sales_info
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10));

-- Create indexes on target table matching partition table index definitions exactly
CREATE INDEX idx_p_sales_info ON p_sales_info (year,month,branch,product);

-- Exchange partition p_ex_sales_info_1 
ALTER TABLE ex_sales_info EXCHANGE PARTITION p_ex_sales_info_1 WITH TABLE p_sales_info INCLUDING INDEXES UPDATE GLOBAL INDEXES;

-- Verify exchange results  
SELECT year,month,branch,product,quantity,amount,salsperson FROM p_sales_info;

YEAR      MONTH     BRANCH    PRODUCT              QUANTITY      AMOUNT SALSPERSON
--------- --------- --------- ----------------- ----------- ----------- -------------------------
2001      01        0201      11001                      30         500 0201010011
2000      12        0102      11001                      20         300
```

<span id="altertableconstraint" name="altertableconstraint" class="yaslink"></span>

### 4. alter\_table\_constraint

This statement is used to specify operations on constraints on the table, including adding constraints, removing constraints, modifying constraints, and disabling or enabling constraints.

<span id="addconstraint" name="addconstraint" class="yaslink"></span>

#### 4.1. add\_constraint

This statement is used to add constraints, allowing multiple constraints to be added within `()` separated by `,`.

##### 4.1.1. out\_of\_line\_constraint

Constraint item, and for a detailed description of the constraint item, please refer to the general SQL syntax [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint).

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS area_constraint; 

CREATE TABLE area_constraint
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

ALTER TABLE area_constraint ADD CONSTRAINT pk_area_no PRIMARY KEY(area_no);
-- or
ALTER TABLE area_constraint ADD PRIMARY KEY(area_no);
```

<span id="dropconstraint" name="dropconstraint" class="yaslink"></span>

#### 4.2. drop\_constraint

This statement is used to remove constraint items.

If a constraint item was created without specifying a name, its default name can be queried from system views (such as USER_CONSTRAINTS) before executing the delete operation.

Deleting a UNIQUE/PRIMARY KEY constraint automatically deletes the corresponding index.

***Example*** for Heap tables

```sql
ALTER TABLE branches DROP CONSTRAINT c_branches_1;

-- Cannot delete primary key constraint when foreign key exists
ALTER TABLE department DROP PRIMARY KEY;
YAS-02188 this unique/primary key is referenced by some foreign keys
```

<span id="modifyconstraint" name="modifyconstrain" class="yaslink"></span>

#### 4.3. modify\_constraint

This statement is used to modify the properties of a constraint item.

##### 4.3.1. Constraint item name

Specify the constraint item to modify its properties, which can be specified in three ways (an error will occur if the specified constraint item does not exist):

- For primary key constraints, it can simply be represented as PRIMARY KEY.
- For unique constraints, it can simply be represented as UNIQUE, but the corresponding columns for this constraint must also be specified.
- As CONSTRAINT constraint name; you can obtain all constraint names on the table from system views (e.g., USER_CONSTRAINTS).

##### 4.3.2. ENABLE|DISABLE

Enable or disable the specified constraint (see the description of ENABLE|DISABLE in [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)).

For constraints with foreign key references in child tables, specifying DISABLE will not be successful unless CASCADE is also specified.

When specifying ENABLE to enable a constraint, if there is existing data in the table that does not satisfy the constraint rules, the enabling will fail unless NOVALIDATE is also specified (but this does not apply to primary key/unique/NOT NULL constraints).

##### 4.3.3. VALIDATE|NOVALIDATE

Enable or disable constraint checks (see the description of VALIDATE|NOVALIDATE in [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)).

This keyword may be omitted; the default for ENABLE operations is VALIDATE and for DISABLE operations is NOVALIDATE.

##### 4.3.4. CASCADE

Specifying CASCADE means that when DISABLEing a constraint item that has foreign key references in child tables, the foreign key constraints in the child tables will also be disabled.

It should be noted that when re-ENABLING the constraint item in the parent table, even if CASCADE is specified, the foreign key constraints in the child table will not be re-enabled; the user must do this manually.

This keyword can be omitted; by default, when DISABLEing a constraint item that has foreign key references in child tables, the disable operation will not be executed on the foreign key constraints in the child tables, resulting in an error message.

***Example***

```sql
-- Disable the primary key constraint on the area table and also disable the foreign key constraint on the child table
ALTER TABLE area MODIFY PRIMARY KEY DISABLE CASCADE;
ALTER TABLE area MODIFY PRIMARY KEY ENABLE;

-- Create a unique constraint on the branches table without enabling it, then enable it
ALTER TABLE branches ADD UNIQUE(branch_no, area_no) DISABLE;
ALTER TABLE branches MODIFY UNIQUE(branch_no, area_no) ENABLE;
```

***Example*** for Heap tables

```sql
-- Disable the foreign key constraint on area_no in branches table
ALTER TABLE branches ADD CONSTRAINT c_branches_1 
FOREIGN KEY (area_no) REFERENCES area(area_no) ON DELETE SET NULL;
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 DISABLE;
-- Modify area_no to a value not existing in area table
UPDATE branches SET area_no='99' WHERE area_no='01';
COMMIT;
-- Enable the area_no foreign key constraint on branches table, but do not enable constraint checks, allowing successful enabling
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 ENABLE NOVALIDATE;
```

<span id="enabledisableconstraint" name="enabledisableconstraint" class="yaslink"></span>

#### 4.4. enable\_disable\_constraint

This statement is used to enable or disable a specific constraint item on the table. It has the same keywords and meanings as [modify_constraint](#modifyconstraint) except for the MODIFY keyword, and the order is different.

***Example*** for Heap tables

```sql
-- Statements in modify_constraint:
ALTER TABLE area MODIFY PRIMARY KEY DISABLE CASCADE;
ALTER TABLE area MODIFY PRIMARY KEY ENABLE;
ALTER TABLE branches MODIFY UNIQUE(branch_no, area_no) ENABLE;
ALTER TABLE branches MODIFY CONSTRAINT c_branches_1 ENABLE NOVALIDATE;

-- In enable_disable_constraint, they can be expressed as:
ALTER TABLE area DISABLE PRIMARY KEY CASCADE;
ALTER TABLE area ENABLE PRIMARY KEY;
ALTER TABLE branches ENABLE UNIQUE(branch_no, area_no);
ALTER TABLE branches ENABLE NOVALIDATE CONSTRAINT c_branches_1;
```

At the same time, this statement also provides the following syntax options related to indexes (for primary key/unique constraints):

- using_index_clause
- KEEP|DROP INDEX

By default, disabling primary key/unique constraints will automatically delete the corresponding index, and enabling primary key/unique constraints will automatically reuse or create an index. The above two options are used to intervene manually in this situation.

##### 4.4.1. using\_index\_clause

When enabling primary key/unique constraints, this syntax can be used to manually specify the index that was automatically reused or created by the system. For detailed syntax definitions and descriptions, see [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint) in using_index_clause.

##### 4.4.2. KEEP|DROP INDEX

When disabling primary key/unique constraints, using KEEP INDEX allows specifying not to delete the corresponding index, while using DROP INDEX follows the default behavior, i.e., deleting the corresponding index.

***Example***

```sql
-- Disable the primary key but keep the corresponding index
ALTER TABLE area DISABLE PRIMARY KEY CASCADE KEEP INDEX;

-- Disable the primary key and also delete the corresponding index
ALTER TABLE area DISABLE PRIMARY KEY CASCADE DROP INDEX;
```

<span id="reclaimtableclause" name="reclaimtableclause" class="yaslink"></span>

### 5. reclaim\_table\_clause

This statement is used to declare storage objects and attributes for migrated objects after tablespace migration.

> **Warn**:
>
> Typically used in ISC Distributed Cluster Deployment scenarios, after scaling in/out of nodes/node groups, to relate logical objects with physical storage objects.
>
> In this scenario, the database will automatically generate reclaim statements and execute them on the target nodes automatically. **It is not recommended for users to execute this statement manually**, as doing so may lead to system crashes or other unpredictable results.

<span id="altertableidentity" name="altertableidentity" class="yaslink"></span>

### 6. alter\_table\_identity

This statement is used to modify or remove the identity property of a column.

***Example*** for Heap tables

```sql
-- Modify identity attribute
ALTER TABLE area MODIFY area_id INTEGER generated BY DEFAULT AS identity(START WITH 1 INCREMENT BY 5);
-- Remove identity attribute
ALTER TABLE area MODIFY area_id DROP IDENTITY;
-- Or
ALTER TABLE area MODIFY area_id INTEGER DROP IDENTITY;
```

<span id="identityclause" name="identityclause" class="yaslink"></span>

#### 6.1. identity\_clause

This statement is used to specify the identity property for a designated column. A table can only have one identity column, which must be of a numeric type.

* GENERATED ALWAYS AS IDENTITY means that the identity column's value is always generated by the sequence when inserting data.   
* GENERATED BY DEFAULT AS IDENTITY means that when inserting data, if the value of this column is not specified, it will be generated by the sequence.  
* GENERATED BY DEFAULT ON NULL AS IDENTITY means that when inserting data, if the value of this column is not specified or is specified as NULL, it will be generated by the sequence.  

<span id="identityoptions" name="identityoptions" class="yaslink"></span>

#### 6.2. identity\_options

This statement is used to set the sequence properties for a specified identity column.   

* START WITH specifies the starting value of the sequence number generator, which must be between MINVALUE and MAXVALUE.  
  If START WITH LIMIT VALUE, you need to first check if the table is empty. If the table is empty, then the starting value is MINVALUE (for ascending sequence number generator) or MAXVALUE (for descending sequence number generator); if the table is not empty, the starting value is the current maximum value in the table + increment (for ascending sequence number generator) or the current minimum value in the table + increment (for descending sequence number generator).  
* INCREMENT BY specifies the increment (Interval) for the sequence number generator, i.e., next sequence number value = current sequence number value + increment. This value cannot be 0, and if omitted, defaults to 1.  
* MAXVALUE/NOMAXVALUE specifies the maximum value for the sequence number generator. For ascending sequence number generators, NOMAXVALUE indicates the maximum positive integer value of the operating system; for descending sequence number generators, NOMAXVALUE indicates -1.  
* MINVALUE/NOMINVALUE specifies the minimum value for the sequence number generator. For ascending sequence number generators, NOMINVALUE indicates 1; for descending sequence number generators, NOMAXVALUE indicates the maximum negative integer value of the operating system.  
* CYCLE/NOCYCLE specifies whether to enable sequence number cycling when the generated sequence number reaches MAXVALUE (for ascending sequence number generator) or MINVALUE (for descending sequence number generator).  
* ORDER/NOORDER specifies whether to guarantee the sequence number is generated in the order requested.  
* CACHE/NOCACHE specifies whether to preallocate sequence numbers in memory; if omitted, defaults to preallocating 20 sequence numbers in memory.  
* NOKEEP/NOSCALE is only a syntax support and has no practical meaning.
