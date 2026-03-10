## General Description

The CREATE TABLE statement is used to create a table object. The table types can be categorized as HEAP tables, TAC tables, and LSC tables. In ISC Distributed Cluster Deployment, this statement can also specify the distribution type of the table object: [sharded table](#shardtable) or [duplicated table](#duplicatetable), with the default being a sharded table.

YashanDB supports specifying the default table type when creating a table object through the configuration of the DEFAULT_TABLE_TYPE parameter (HEAP|TAC|LSC), and this parameter allows for online switching. Additionally, the table type can be specified during the creation of the table object using the [ORGANIZATION](#organizationclause) syntax. Once the table object is created successfully, the table type cannot be modified.

|Type |Storage Method |Storage Structure |Applicable Deployment Forms |
| ------------------ | -------- | ---------- | -------------------------------------- |
| HEAP Table          | Row Storage    | Segment Page Structure | <li>Standalone Deployment</li><li>YAC/Distributed Cluster Deployment</li><li>ISC Distributed Cluster Deployment</li> |
| LSC Table           | Column Storage  | Column Storage Structure | <li>Standalone Deployment</li><li>ISC Distributed Cluster Deployment</li> |
| TAC Table           | Column Storage  | Segment Page Structure | <li>Standalone Deployment</li><li>ISC Distributed Cluster Deployment</li> |

In analyzing business scenarios, it is recommended to use LSC tables.

Before creating an LSC table, it must be ensured that the tablespace it resides in has mounted a bucket (data bucket). Regular users created with the [CREATE USER](CREATE USER) statement will have their default tablespace automatically mounted with a bucket, allowing them to create LSC tables directly. System users, however, may vary based on product architecture:

-  In Standalone Deployment, the SYSTEM tablespace does not mount a bucket by default, and system users (such as SYS) cannot create LSC tables directly; they must first execute the [ALTER TABLESPACE](ALTER TABLESPACE) statement to mount a bucket for the SYSTEM tablespace before creating the LSC table.
- In ISC Distributed Cluster Deployment, since the table objects created by system users default to the users' tablespace and the users' tablespace is also mounted with a bucket by default, they can create LSC tables directly without extra operations.

> **Note**: 
>
> In actual production environments, it is recommended to plan the tablespace and users reasonably before creating business tables; please refer to [CREATE TABLESPACE](CREATE TABLESPACE) and [CREATE USER](CREATE USER) for related statements.

## Statement Definition

**create table::=**

```ebnf+diagram
syntax::= CREATE [(GLOBAL|PRIVATE) TEMPORARY|SHARDED|DUPLICATED] TABLE [IF NOT EXISTS] [schema "."] table_name ( 
( "("  relation_properties ")"
[table_properties]
[lsc_table_properties]
[row_movement_clause] ) 
| object_table )
```

**[relation\_properties](#relationproperties)::=**

```ebnf+diagram
syntax::= (column_definition|out_of_line_constraint) 
{"," (column_definition|out_of_line_constraint)}
```

**[object\_table](#objecttable)::=**

```ebnf+diagram
syntax::= OF [schema "." object_type]
```

**[out_of_line_constraint Definition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)**

**[column\_definition](#columndefinition)::=**

```ebnf+diagram
syntax::= column dataType [VISIBLE|INVISIBLE] [identity_clause] [(DEFAULT default_expr|codec_expr|inline_constraint)
{" " (DEFAULT default_expr|codec_expr|inline_constraint)}]
```

**[identity_clause](#identityclause)::=**

```ebnf+diagram
syntax::= GENERATED [ALWAYS |(BY DEFAULT [(ON NULL)])] AS IDENTITY [identity_options]
```

**[identity_options](#identityoptions)::=**

```ebnf+diagram
syntax::= ["("] [(((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE)) 
{" " (((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)|(NOKEEP)|(NOSCALE))}] [")"] 
```

**[codec_expr](#codecexpr)::=**

```ebnf+diagram
syntax::= [compression_clause] [encoding_clause] [column_encryption_clause]
```

**[compression_clause](#compressionclause)::=**

```ebnf+diagram
syntax::= COMPRESSION (compression_type (HIGH|MEDIUM|LOW) | UNCOMPRESSED)
```

**[compression_type](#compressiontype)::=**

```ebnf+diagram
syntax::= (LZ4|ZSTD)
```

**[encoding_clause](#encodingclause)::=**

```ebnf+diagram
syntax::= ENCODING (PLAIN|RLE|DICTIONARY"("(RLE|(PLAIN ["," CARDINALITY]))")"|"BYTE-PACKED")
```

**[column_encryption_clause](#columnencryptionclause)::=**

```ebnf+diagram
syntax::= ENCRYPT [USING  encryption_algorithm]
```

**[inline_constraint Definition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint)**

**[table\_properties](#tableproperties)::=**

```ebnf+diagram
syntax::= (organization_clause
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
|object_affinity_clause)}
```

**[organization_clause](#organizationclause)::=**

```ebnf+diagram
syntax::= ORGANIZATION (HEAP|TAC|LSC|EXTERNAL external_table_clause)
```

**[external_table_clause Definition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/external_table)**

**[temp\_table\_attr\_clause](#temptableattrclause)::=**

```ebnf+diagram
syntax::= ON COMMIT (((DROP|PRESERVE) DEFINITION)|((DELETE|PRESERVE) ROWS)) 
```

**[physical\_attribute\_clause](#physicalattributeclause)::=**

```ebnf+diagram
syntax::= ((TABLESPACE tablespace|TABLESPACE SET tablespace_set)|PCTFREE integer|PCTUSED integer|INITRANS integer|MAXTRANS integer|storage_clause|deferred_segment_creation)
```

**[storage_clause Definition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/storage_clause)**

**[deferred\_segment\_creation](#deferredsegmentcreation)::=**

```ebnf+diagram
syntax::= SEGMENT CREATION (IMMEDIATE | DEFERRED) 
```

**[table\_partition\_clause](#tablepartitionclause)::=**

```ebnf+diagram
syntax::= (range_partitions
|list_partitions
|hash_partitions
|composite_range_partitions
|composite_list_partitions
|composite_hash_partitions
|consistent_hash_partitions
|consistent_hash_with_subpartitions)
```

**[range\_partitions](#rangepartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY RANGE "(" ((column) {"," (column)}) ")" [interval_clause] 
"(" (PARTITION [partname]  range_values_clause  [table_partition_description | lob_clauses])
{"," (PARTITION [partname]  range_values_clause  [table_partition_description | lob_clauses])} ")"
```

**[interval_clause](#intervalclause)::=**

```ebnf+diagram
syntax::= INTERVAL "(" expr ")" [STORE IN "(" ((tablespace) {"," (tablespace)}) ")"]
```

**[range\_values\_clause](#rangevaluesclause)::=**

```ebnf+diagram
syntax::= VALUES LESS THAN "(" (literal|MAXVALUE) {"," (literal|MAXVALUE)} ")"
```

**[table\_partition\_description](#tablepartitiondescription)::=**

```ebnf+diagram
syntax::= (TABLESPACE tablespace|PCTFREE integer|PCTUSED integer|INITRANS integer|MAXTRANS integer|deferred_segment_creation|object_affinity_clause)
```

**[list\_partitions](#listpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY LIST "(" (column) {"," (column)} ")" "(" (PARTITION [partname] 
list_values_clause  [table_partition_description | lob_clauses])
{"," (PARTITION [partname]  list_values_clause  [table_partition_description | lob_clauses])} ")"
```

**[list\_values\_clause](#listvaluesclause)::=**

```ebnf+diagram
syntax::= VALUES "(" (DEFAULT|list_values) ")"
```

**[list\_values](#listvaluesclause)::=**

```ebnf+diagram
syntax::= ((literal|NULL) {"," (literal|NULL)})
|(("(" ((literal|NULL) {"," (literal|NULL)})")") {"," ("(" ((literal|NULL) {"," (literal|NULL)})")")})
```

**[hash\_partitions](#hashpartitions)::=**

~~~ebnf+diagram
syntax::= PARTITION BY HASH "(" ((column) {"," (column)}) ")"
(individual_partition_clause | hash_partitions_by_quantity)
~~~

**[individual\_partition\_clause](#individualpartitionclause)::=**

~~~ebnf+diagram
syntax::= "(" ( PARTITION [partname]  [partition_storage_clause | lob_clauses] )
{"," ( PARTITION [partname]  [partition_storage_clause | lob_clauses] )}")"
~~~

**[partition\_storage\_clause](#partitionstorageclause)::=**

```ebnf+diagram
syntax::= TABLESPACE tablespace
```

**[hash\_partitions\_by\_quantity](#hashpartitionsbyquantity)::=**

~~~ebnf+diagram
syntax::= PARTITIONS hash_partition_quantity [STORE IN "(" ((tablespace) {"," (tablespace)}) ")"] 
[OVERFLOW STORE IN "(" ((tablespace) {"," (tablespace)}) ")"]
~~~

**[composite\_range\_partitions](#compositerangepartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY RANGE "(" ((column) {"," (column)}) ")"
(subpartition_by_range|subpartition_by_list|subpartition_by_hash)
"(" ((range_partition_desc) {"," (range_partition_desc)}) ")"
```

**[subpartition_by_range](#subpartitionbyrange)::=**

```ebnf+diagram
syntax::= SUBPARTITION BY RANGE "(" ((column) {"," (column)}) ")" [subpartition_template]
```

**[subpartition_template](#subpartitiontemplate)::=**

```ebnf+diagram
syntax::= SUBPARTITION TEMPLATE (("(" (
((range_subpartition_desc) {"," (range_subpartition_desc)})
| ((list_subpartition_desc) {"," (list_subpartition_desc)})
| (individual_hash_subparts { "," individual_hash_subparts})) ")"))
```

**[range\_subpartition_desc](#rangesubpartitiondesc)::=**

```ebnf+diagram
syntax::= (SUBPARTITION [subpartname] range_values_clause [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation])
```

**[list\_subpartition_desc](#listsubpartitiondesc)::=**

```ebnf+diagram
syntax::= (SUBPARTITION [subpartname] list_values_clause [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation])
```

**[individual_hash_subparts](#individualhashsubparts)::=**

```ebnf+diagram
syntax::= SUBPARTITION [subpartname] [TABLESPACE tablespace | lob_clauses] [deferred_segment_creation]
```

**[subpartition_by_list](#subpartitionbylist)::=**

```ebnf+diagram
syntax::= SUBPARTITION BY LIST "(" ((column) {"," (column)}) ")" [subpartition_template]
```

**[subpartition_by_hash](#subpartitionbyhash)::=**

```ebnf+diagram
syntax::= SUBPARTITION BY HASH "(" ((column) {"," (column)}) ")" 
[(subpartition_template | hash_subparts_by_quantity)]
```

**[hash_subparts_by_quantity](#hashsubpartsbyquantity)::=**

```ebnf+diagram
syntax::= SUBPARTITIONS integer [STORE IN "(" ((tablespace) {"," (tablespace)}) ")"]
```

**[range\_partition\_desc](#rangepartitions)::=**

```ebnf+diagram
syntax::= PARTITION [partname]  range_values_clause  [table_partition_description]
[hash_subparts_by_quantity | ("(" (
((range_subpartition_desc) {"," (range_subpartition_desc)})
| ((list_subpartition_desc) {"," (list_subpartition_desc)})
| ((individual_hash_subparts) {"," (individual_hash_subparts)})) ")")]
```

**[composite\_list\_partitions](#compositelistpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY LIST "(" ((column) {"," (column)}) ")"
(subpartition_by_range | subpartition_by_list | subpartition_by_hash)
"(" ((list_partition_desc) {"," (list_partition_desc)}) ")"
```

**[list\_partition\_desc](#listpartitions)::=**

```ebnf+diagram
syntax::= PARTITION [partname]  list_values_clause [table_partition_description]
[hash_subparts_by_quantity | ("(" (
((range_subpartition_desc) {"," (range_subpartition_desc)})
| ((list_subpartition_desc) {"," (list_subpartition_desc)})
| ((individual_hash_subparts) {"," (individual_hash_subparts)})) ")")]
```

**[composite\_hash\_partitions](#compositehashpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY HASH "(" ((column) {"," (column)}) ")"
(subpartition_by_range | subpartition_by_list | subpartition_by_hash)
(("(" ((hash_partition_desc) {"," (hash_partition_desc)}) ")") | hash_partitions_by_quantity)
```

**[hash\_partition\_desc](#hashpartitiondesc)::=**

```ebnf+diagram
syntax::= PARTITION [partname] [table_partition_description]
["(" (
((range_subpartition_desc) {"," (range_subpartition_desc)})
| ((list_subpartition_desc) {"," (list_subpartition_desc)})
| ((individual_hash_subparts) {"," (individual_hash_subparts)})) ")"]
```

**[consistent_hash_partitions](#consistenthashpartitions)::=**

~~~ebnf+diagram
syntax::= PARTITION BY [CONSISTENT] HASH "(" ((column) {"," (column)}) ")"
(individual_partition_clause | hash_partitions_by_quantity)
~~~

**[consistent_hash_with_subpartitions](#consistenthashwithsubpartitions)::=**

~~~ebnf+diagram
syntax::= PARTITION BY [CONSISTENT] HASH "(" ((column) {"," (column)}) ")"
(subpartition_by_range|subpartition_by_list|subpartition_by_hash)
(("(" ((hash_partition_desc) {"," (hash_partition_desc)}) ")") | hash_partitions_by_quantity)
~~~

**[lob\_clauses](#lobclause)::=**

```ebnf+diagram
syntax::= (lob_clause) {" " (lob_clause)}
```

**[lob\_clause](#lobclause)::=**

```ebnf+diagram
syntax::= LOB "("(column) {"," (column)} ")" STORE AS [BASICFILE|SECUREFILE]
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
    )}
")"
```

**[logging\_clause](#loggingclause)::=**

```ebnf+diagram
syntax::= LOGGING | NOLOGGING
```

**[parallel\_clause](#parallelclause)::=**

```ebnf+diagram
syntax::= NOPARALLEL | PARALLEL [integer]
```

**[cache\_clause](#cacheclause)::=**

```ebnf+diagram
syntax::= CACHE | NOCACHE
```

**[readonly\_clause](#readonlyclause)::=**

```ebnf+diagram
syntax::= READONLY | READWRITE
```

**[inmemory\_clause](#inmemoryclause)::=**

```ebnf+diagram
syntax::= INMEMORY | NO INMEMORY
```

**[table\_compression](#tablecompression)::=**

```ebnf+diagram
syntax::= COMPRESS | NOCOMPRESS
```

**[nested_table_clauses](#nestedtableclause)::=**

```ebnf+diagram
syntax::= (nested_table_clause) {" " (nested_table_clause)}
```

**[nested_table_clause](#nestedtableclause)::=**

```ebnf+diagram
syntax::= NESTED TABLE (nested_item|COLUMN_VALUE) [LOCAL|GLOBAL] STORE AS 
storage_table ["(" nested_table_clause ")"] ["(" TABLESPACE tablespace_name ")"]
```

**[table_encryption_clause](#tableencryptionclause)::=**

```ebnf+diagram
syntax::= ENCRYPT [USING ("'" AES128 "'" | "'" SM4 "'")]
```

**[object_affinity_clause](#objectaffinityclause)::=**

```ebnf+diagram
syntax::= OBJECT AFFINITY (default|INSTANCE instance_number|auto)
```

**[lsc_table_properties](#lsctableproperties)::=**

```ebnf+diagram
syntax::= [compression_clause] [table_sort_clause] [mcol_ttl_clause] [transformer_clause] [mcolability_clause] storage_table
```

**[table_sort_clause](#tablesortclause)::=**

```ebnf+diagram
syntax::= ORDER BY "("(column_name){"," (column_name)}")" [NULLS (FIRST|LAST)] [ASC|DESC] [SCOL]
```

**[mcol_ttl_clause](#mcolttlclause)::=**

```ebnf+diagram
syntax::= MCOL TTL timestamp
```

**[transformer_clause](#transformerclause)::=**

```ebnf+diagram
syntax::= (ENABLE|DISABLE) ((TRANSFORM|COMPACT|BUILD AC) {(TRANSFORM|COMPACT|BUILD AC)})
```

**[mcolability_clause](#mcolabilityclause)::=**

```ebnf+diagram
syntax::= (ENABLE|DISABLE) (MCOL)
```

**[row\_movement\_clause](#rowmovementclause)::=**

```ebnf+diagram
syntax::= (ENABLE|DISABLE) ROW MOVEMENT
```

<span id="temptable" name="temptable" class="yaslink"></span>

### 1. GLOBAL TEMPORARY

This statement only applies to HEAP tables and standalone TAC tables, used to specify that the created table is a global temporary table. A global temporary table is visible to all sessions in the database, but the data within the table is isolated between sessions, meaning each session can only see the data inserted within that session.

Once created, a global temporary table will persist (unless dropped). The ON COMMIT DELETE | PRESERVE ROWS clause defines whether its data is retained after transaction commits, detailed in [temp_table_attr_clause](#temptableattrclause).

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
CREATE GLOBAL TEMPORARY TABLE globaltemtable(c1 INT,c2 INT);
```

<span id="privatetemptable" name="privatetemptable" class="yaslink"></span>

### 2. PRIVATE TEMPORARY

This statement only applies to HEAP tables and standalone TAC tables, used to specify that the created table is a private temporary table. A private temporary table created in a session is only visible to that session.

The data in a private temporary table is retained throughout the current session and will not be deleted upon transaction commit. The ON COMMIT DROP | PRESERVE DEFINITION clause specifies whether to drop the table after transaction commit, detailed in [temp_table_attr_clause](#temptableattrclause).

The name of a private temporary table must start with `YAS$PTT_` or `ORA$PTT_`; other table names cannot start with `YAS$PTT_` or `ORA$PTT_`.

Private temporary tables do not allow constraints to be created.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
CREATE PRIVATE TEMPORARY TABLE YAS$PTT_privatetemtable(c1 INT,c2 INT);

-- If it does not start with YAS$PTT_ or ORA$PTT_, an error will be returned
CREATE PRIVATE TEMPORARY TABLE privatetemtable(c1 INT,c2 INT);
YAS-02170 create a private temporary table with a name not matching 'YAS$PTT_' prefix
```

<span id="shardtable" name="shardtable" class="yaslink"></span>

### 3. SHARDED

This statement is used only in ISC Distributed Cluster Deployment and indicates the creation of a sharded table. In a sharded table, data is partitioned and stored based on chunks on each DN.

YashanDB treats each partition of a sharded table as a chunk, and the data inserted into the table will be allocated to each chunk according to a specified rule. This allocation rule can be defined using the [consistent_hash_partitions](#consistenthashpartitions) statement. If no rule is specified, the table will be hash-partitioned using the following rules:

- If a primary key column is defined on the table, the partition key column will be the primary key column.
- If no primary key column is defined but there exists a unique index, the partition key will be the first appropriate datatype field in the common subset of all unique indexes.
- Otherwise, the partition key will be the first column field of an appropriate datatype in the table.

SHARDED is the default table creation method for YashanDB ISC distributed databases, i.e., CREATE TABLE = CREATE SHARDED TABLE.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Create a default sharded table and insert records
CREATE SHARDED TABLE area_shard
(area_no CHAR(2) NOT NULL,
area_name VARCHAR2(60),
DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

-- Query table attributes, sharded=Y indicates a sharded table 
SELECT table_name,table_type,partitioned,SHARDED,DUPLICATED 
FROM USER_TABLES 
WHERE table_name='AREA_SHARD';
TABLE_NAME                 TABLE_TYPE PARTITIONED SHARDED DUPLICATED
-------------------------- ---------- ----------- ------- ----------
AREA_SHARD                 LSC        N           Y       N

-- Query the number of table partitions, that is the number of chunks
SELECT COUNT(1) 
FROM USER_TAB_PARTITIONS 
WHERE table_name='AREA_SHARD';
             COUNT(1)
---------------------
                    6
```

<span id="duplicatetable" name="duplicatetable" class="yaslink"></span>

### 4. DUPLICATED

This statement is used only in ISC Distributed Cluster Deployment and indicates the creation of a duplicated table. The data inserted into a duplicated table will be completely copied to each DN.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Create a duplicated table 
CREATE DUPLICATED TABLE area_dupli
(area_no CHAR(2) NOT NULL,
area_name VARCHAR2(60),
DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
 
-- Query table attributes, duplicated=Y indicates a duplicated table 
SELECT table_name,table_type,partitioned,SHARDED,DUPLICATED 
FROM USER_TABLES 
WHERE table_name='AREA_DUPLI';
TABLE_NAME                 TABLE_TYPE PARTITIONED SHARDED DUPLICATED
-------------------------- ---------- ----------- ------- ----------
AREA_DUPLI                 LSC        N           N       Y
```

### 5. IF NOT EXISTS

This clause is used to determine if the table exists before creating it; if omitted, no check will be performed. If included and the table to be created already exists, the system will not return an error and will use the existing table without creating a new one.

***Example***

```sql
-- When creating an already existing table area, no error will be returned, and the new table will not be successfully created
CREATE TABLE IF NOT EXISTS area(c1 INT, c2 INT);

SELECT * FROM area WHERE area_no='01';
AREA_NO AREA_NAME        DHQ       
------- ---------------- ----------
01      EastChina           Shanghai    
```

### 6. table\_name

This clause is used to specify the name of the table being created. It cannot be omitted and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

If the table name contains a `.` symbol, it indicates that the part before the symbol is the username and the part after is the table name. In this case, privileges to create table objects under the specified username must be possessed.

<span id="objecttable" name="objecttable" class="yaslink"></span>

### 7. object\_table

Create an object table corresponding to object_type explicitly using the OF clause. The columns of the object table correspond one-to-one with the attributes of object_type.

> **Caution**: 
>
> The object table functionality is an experimental feature and **is not recommended for use in production environments** to avoid affecting system stability.

<span id="relationproperties" name="relationproperties" class="yaslink"></span>

### 8. relation\_properties

This clause is used to specify the structure of the table to be created, including column fields and constraints.

<span id="columndefinition" name="columndefinition" class="yaslink"></span>

#### 8.1. column\_definition

This clause is used to define the column fields of the table.

##### 8.1.1. column

The name of the column field, which cannot be omitted and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

##### 8.1.2. dataType

Specifies the data type of the column field. Please refer to YashanDB's [data type descriptions](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/00Data Types).

When specifying a LOB/JSON type for the data type, storage properties can be specified using the [lob_clauses](#lobclause) clause.

When specifying a Nested Table UDT type, it must create a nested table through the [nested_table_clauses](#nestedtableclause) clause, and the Nested Table UDT type cannot be specified for columns of [temporary tables](#temptable).

##### 8.1.3. VISIBLE|INVISIBLE

This keyword is used to specify whether a column field is visible or not. The default value is VISIBLE.  

The usage constraints for invisible columns (also known as "hidden columns") are as follows:  

- Applies only to HEAP tables and cannot be used for temporary or external tables.  

- The data type of an invisible column cannot be UDT.

- A table cannot consist entirely of invisible columns; there must be at least one visible column.

- When operating on an invisible column, the column name must be explicitly specified, including in SELECT, INSERT, import/export, etc. For example, the `SELECT * FROM` statement cannot query invisible columns.  

***Example*** for Heap tables

```sql
-- Create a table containing an invisible column 
CREATE TABLE product
(product_no CHAR(5) PRIMARY KEY,
 product_name VARCHAR2(30),
 cost NUMBER INVISIBLE,
 price NUMBER
);
-- Direct insertion of data will result in an error
INSERT INTO product VALUES ('11001','product001',8,10);

YAS-04304 the count of column is mismatched

-- When inserting data, the name of the invisible column must be explicitly specified 
INSERT INTO product VALUES ('11001','product001',10);
INSERT INTO product (product_no,product_name,cost,price) VALUES ('11002','product002',13,16);

-- The `SELECT * FROM` statement cannot query the invisible `cost` column
SELECT * FROM product;

PRODUCT_NO PRODUCT_NAME                            PRICE
---------- --------------------------------- -----------
11001      product001                                 10
11002      product002                                 16

-- Explicitly specifying the invisible column name ensures a successful query
SELECT product_name,cost FROM product;

PRODUCT_NAME                             COST
--------------------------------- -----------
product001
product002                                 13
```

<span id="identityclause" name="identityclause" class="yaslink"></span>

##### 8.1.4. identity\_clause

This clause is used to specify the identity properties for a given column. A table can only have one identity column, which must be of a numeric type.

* GENERATED ALWAYS AS IDENTITY indicates that the identity column's values are always generated by a sequence during data insertion.  
* GENERATED BY DEFAULT AS IDENTITY indicates that if the value for this column is not specified during data insertion, it will be generated by a sequence.  
* GENERATED BY DEFAULT ON NULL AS IDENTITY indicates that if the value for this column is not specified or if it is set to NULL during data insertion, it will be generated by a sequence.  

<span id="identityoptions" name="identityoptions" class="yaslink"></span>

###### 8.1.4.1. identity\_options

This clause is used to specify the sequence attributes for the designated identity column.  

* START WITH specifies the starting value of the sequence generator, which must be between MINVALUE and MAXVALUE. If omitted, it defaults to 1 for ascending sequence generators or -1 for descending sequence generators.  
* INCREMENT BY specifies the interval for the sequence generator, i.e., the next sequence number = the current sequence number + the interval. This value cannot be 0; if omitted, it defaults to 1.  
* MAXVALUE/NOMAXVALUE specifies the maximum value for the sequence generator. For ascending sequence generators, NOMAXVALUE represents the maximum positive integer of the operating system; for descending sequence generators, NOMAXVALUE represents -1.  
* INVALUE/NOMINVALUE specifies the minimum value for the sequence generator. For ascending sequence generators, NOMINVALUE represents 1; for descending sequence generators, NOMINVALUE represents the maximum negative integer of the operating system.  
* CYCLE/NOCYCLE specifies whether to enable sequence cycling when the generated sequence number reaches MAXVALUE (for ascending sequence generators) or MINVALUE (for descending sequence generators).  
* ORDER/NOORDER specifies whether to guarantee the generation of sequence numbers in the order of requests.  
* CACHE/NOCACHE specifies whether to preallocate sequence numbers in memory; if omitted, the default is to preallocate 20 sequence numbers in memory.  
* NOKEEP/NOSCALE is for syntax support only, and has no practical significance.  

***Example*** for Heap tables

```sql
-- generated always
CREATE TABLE area(
    area_id NUMBER generated always AS identity,
    area_name VARCHAR2(60),
    DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL
);

-- generated by default on null
CREATE TABLE area(
    area_id NUMBER generated BY DEFAULT ON null AS identity(START WITH 100 INCREMENT BY 1),
    area_name VARCHAR2(60),
    DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL
);
```

##### 8.1.5. DEFAULT default\_expr

The default value for the column field. The default_expr can be a literal, expression, function, etc.

Default values are not allowed for XMLTYPE columns.

<span id="codecexpr" name="codecexpr" class="yaslink"></span>

##### 8.1.6. codec\_expr

For columns of LSC tables, this clause defines the encoding and compression properties of the column. The system will store data in accordance with this setting. 

For columns of standalone TAC tables, this clause defines the dictionary encoding properties of the column.

<span id="compressionclause" name="compressionclause" class="yaslink"></span>

###### 8.1.6.1. compression\_clause

This clause is used to define the compression properties for columns in LSC tables.

- Compression algorithms: can be specified as LZ4 or ZSTD compression algorithms, or specified as UNCOMPRESSED to not compress the data.
- Compression Level: after specifying the compression algorithm as LZ4 or ZSTD, it can be set to compress at one of three levels: LOW, MEDIUM, or HIGH. If no level is specified, it will be defaulted based on the configuration item COMPRESSION_LEVEL.

Regarding the prioritization between the compression property defined at the column level and the default compression property of the system for the data of a specific column:

Column-level Compression Property > Table-level Compression Property > System Parameter COMPRESSION and its related configuration of COMPRESSION_LEVEL.

You are encouraged to select a suitable compression algorithm based on the database usage scenario. The advantages of the LZ4 compression algorithm are: fast decompression and compression speed, along with good compression performance. The ZSTD compression algorithm, compared to LZ4, has slower compression and decompression speeds but offers better compression results.

- If you are unsure about the usage scenario for a table, it is recommended to default to LZ4 LOW for compression, as in most scenarios, LZ4 can use less CPU resources and reduce IO waiting times.
- For environments requiring high query performance, you are advised to use LZ4 for compression, with compression levels adjusted based on your requirements for data import and cold-to-hot data conversion.
- For archived data with more relaxed query performance requirements, ZSTD compression is recommended, with levels adjusted based on your import speed requirements.

YashanDB supports defining compression properties at the table level or column level and provides configuration parameters to set the system's default compression properties.

<span id="compressiontype" name="compressiontype" class="yaslink"></span>

**compression_type**

This clause is used to specify the compression algorithm and compression levels.

- COMPRESSION: specifies whether to compress the data.
- COMPRESSION_LEVEL: specifies the level of data compression.

<span id="encodingclause" name="encodingclause" class="yaslink"></span>

###### 8.1.6.2. encoding\_clause

For LSC table column definitions, this clause specifies encoding properties: 

- PLAIN encoding: no encoding, data is stored in its original state.
- RLE encoding: Run-Length Encoding.
- DICTIONARY (PLAIN): data encoded with a dictionary is stored using PLAIN encoding.
- DICTIONARY (RLE): data encoded with a dictionary is stored using RLE encoding.
- BYTE-PACKED: data is encoded based on byte length.

If no encoding is specified, adaptive encoding is used by default, where the system automatically detects suitable encoding based on data types and characteristics. Different algorithms may be applied to the same column across different data blocks.

The appropriateness of encoding for specific columns is related to the data type and characteristics of the data. Below is YashanDB's compatibility for different data types:

|Data Type |PLAIN encoding |RLE encoding |Dictionary Encoding |BYTE-PACKED encoding |
| -------------------------------- | --------- | ------- | -------- | --------    |
| TINYINT/SMALLINT/LOB types     | ✓              | X            | X                   | X                     |
| INT/BIGINT/FLOAT/DOUBLE/datetime| ✓              | ✓            | ✓                   | X                     |
| Character type                  | ✓              | X            | ✓                   | X                     |
| NUMBER type                     | ✓              | X            | X                   | ✓                     |

For BOOLEAN-type data, the encoding is handled automatically by the system, and there is no need to specify an encoding method. It is advisable to avoid dictionary encoding for CHAR fields longer than 128 bytes due to high memory consumption.

***Example*** for LSC tables

```sql
-- Create LSC table, compressing specified columns and storing them with dictionary encoding
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

For TAC table column definitions, the dictionary encoding property is specified using DICTIONARY(PLAIN), which only supports character type columns. 

Additionally, TAC table columns using this property can specify a maximum limit for dictionary values, ranging from [1,65535], with the default dictation limit being 65535.

***Example*** for Standalone Deployment TAC tables

```sql
-- Create TAC table, specifying dictionary encoding for designated columns and a maximum limit of 100 for the dictionary
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

<span id="columnencryptionclause" name="columnencryptionclause" class="yaslink"></span>

###### 8.1.6.3. column\_encryption\_clause

This clause is used to define the encryption properties of a specified column.

The regulations for column encryption are as follows:

-  In Standalone/YAC/Distributed Cluster Deployment, relevant configuration for key management must be completed before creating tables with encrypted columns, including creating wallets, opening wallets, and setting master keys. For further details, please refer to [Configuring Wallets](../../All Manuals/Product Security/Encryption/Storage Encryption/Key Management.html#configuringwallet).
- The sys user cannot perform column encryption.
- This is only applicable for HEAP and LSC tables, and cannot be specified for [temporary tables](#temptable).
- The target column to be encrypted must meet the following requirements:
    - If there is an index to be created on that column, it can only be specified as an equi-join BTREE index.
    - The column cannot be a partition key or subpartition key.
    - The column cannot be a column that is referenced by [AC objects](CREATE ACCESS CONSTRAINT).
    - The column cannot be a foreign key column or a dependent key of a foreign key.
    - If it is an LSC table's column encryption, the data type of that column cannot be of LOB type.
- Different columns in the same table must use the same encryption algorithm.
- When using column encryption and [table-level encryption](#tableencryptionclause), both specified algorithms must be the same.
- The column encryption properties once specified cannot be modified, and other properties of the encrypted column cannot be changed.
- For stable data columns in LSC tables, if both column encryption and tablespace encryption are specified, the target column will adopt the column encryption algorithm.

**ENCRYPT**

Enables column encryption functionality.

**USING encryption_algorithm**

This clause specifies the encryption algorithm, supporting AES128 and SM4, which can be omitted. If omitted, the default algorithm used is AES128.

> **Note**:
>
> To use the national secret algorithm SM4 or TDE-related key management functionalities, please refer to [Dependency Preparation](../../All Manuals/Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to ensure that the necessary tools are installed in the server system.

***Example*** for Heap tables and LSC tables

```sql
-- Create an employee information table and encrypt the employee_ID column
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

<span id="inlineconstraint" name="inlineconstraint" class="yaslink"></span>

##### 8.1.7. inline\_constraint

This clause is used to define inline constraint items of the table. For detailed descriptions of constraints, please refer to General SQL syntax [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint). 

Foreign key constraints cannot be established for [temporary tables](#temptable), nor can any foreign key constraints to temporary tables be established from other tables.

For constraint items defined for tables other than LSC tables, parallel constraint checking is enabled by default.

When setting the degree of parallelism, YashanDB automatically detects the current data volume of the table. If the detected data volume is greater than 1G but less than the current value of the DATA_BUFFER_SIZE parameter, a concurrent check of constraints will be conducted with a degree of parallelism equal to half the number of CPUs on the server.

#### 8.2. out\_of\_line\_constraint

This clause is used to define out-of-line constraint items of the table. For detailed descriptions of out-of-line constraints, please refer to the General SQL syntax [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint). 

Foreign key constraints cannot be established for [temporary tables](#temptable), nor can any foreign key constraints to temporary tables be established from other tables.

<span id="tableproperties" name="tableproperties" class="yaslink"></span>

### 9. table\_properties

This clause is used to specify various properties of the table being created; multiple items are separated by a space.

<span id="organizationclause" name="organizationclause" class="yaslink"></span>

#### 9.1. organization\_clause

This clause specifies the storage method of the table data (table type). It can be omitted; if omitted, the system will create a corresponding type of table based on the value of the DEFAULT_TABLE_TYPE parameter. YashanDB's data storage method for different table types can be referenced in the product description [storage engine](../../All Manuals/Product Concepts/YashanDB System Architecture/Storage Engine).

##### 9.1.1. HEAP

Creates a HEAP table. 

##### 9.1.2. TAC

Creates a TAC LSC table.

##### 9.1.3. LSC

Creates an LSC LSC table; this type of table requires the default or specified tablespace to be a databucket tablespace; otherwise, creation will fail.

##### 9.1.4. EXTERNAL

Creates an external table. An external table is a special type of table structure that is stored in the database but has its data stored outside the database. Since the data of an external table is not stored inside the database, all DDL operations based on rows (such as indexing, row migration, flashback, encryption, etc.) do not apply to external tables.

Data for external tables can only be queried, not inserted, updated, or deleted.

Regarding external tables, settings for segment properties are meaningless and will not take effect.

###### 9.1.4.1. external\_table\_clause

The definition of the external data structure corresponding to the external table, specifically referenced in General Syntax [EXTERNAL_TABLE](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/external_table).

This clause can be omitted; if omitted, the created external table will not specify the corresponding external data structure, and the table will not be queried properly.

<span id="temptableattrclause" name="temptableattrclause" class="yaslink"></span>

#### 9.2. temp\_table\_attr\_clause

This clause only applies to [global temporary table](#temptable) and [private temporary table](#privatetemptable), and DELETE|PRESERVE ROWS is only applicable to global temporary tables, while DROP|PRESERVE DEFINITION is only applicable to private temporary tables.

If this clause is omitted, the default values are DELETE ROWS and DROP DEFINITION, respectively.

> **Note**:  
> When the temporary table is set with the DELETE ROWS attribute, if data is inserted into the table first, and then an autonomous transaction is opened, and an operation of commit is performed within the autonomous transaction, the data inserted within the autonomous transaction will not be cleared.

##### 9.2.1. ON COMMIT DELETE|PRESERVE ROWS

Specifies whether to retain the data of the global temporary table inserted in the current session after the transaction commit, DELETE means to delete, and PRESERVE means to retain.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- Create a global temporary table T_orders_info and specify PRESERVE ROWS
CREATE GLOBAL TEMPORARY TABLE T_orders_info (
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10))
ON COMMIT PRESERVE ROWS;
  
-- Insert data and commit
INSERT INTO T_orders_info VALUES ('20010102020001','product 001','02','0202',SYSDATE-400,'0001');
COMMIT;
  
-- Query results: since PRESERVE ROWS is specified, the data (only the data inserted for this session) can still be queried. If DELETE ROWS were specified, the data could not be queried.
SELECT order_no,order_desc,area,branch,order_date,salesperson FROM T_orders_info;
ORDER_NO          ORDER_DESC     AREA  BRANCH ORDER_DATE             SALESPERSON 
----------------- -------------- ----- ------ ---------------------- -------------
20010102020001    product 001    02    0202   2020-12-06 22:55:32    0001
```

##### 9.2.2. ON COMMIT DROP|PRESERVE DEFINITION

Specifies whether the private temporary table created in the current session will be deleted after the transaction commit, DROP means delete, and PRESERVE means retain.

***Example*** for Standalone Deployment Heap tables and TAC tables

```sql
-- Create a private temporary table YAS$PTT_orders_info and specify DROP DEFINITION
CREATE PRIVATE TEMPORARY TABLE YAS$PTT_orders_info (
order_no CHAR(14),
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE,
salesperson CHAR(10))
ON COMMIT DROP DEFINITION;
  
-- Insert data and commit
INSERT INTO YAS$PTT_orders_info VALUES ('20010102020001','product 001','02','0202',SYSDATE-400,'0001');
COMMIT;
  
-- Query results: since DROP DEFINITION is specified, the PTT_orders_info table will be deleted after transaction commit
SELECT * FROM YAS$PTT_orders_info;
[1:15]YAS-02012 table or view does not exist
```

<span id="physicalattributeclause" name="physicalattributeclause" class="yaslink"></span>

#### 9.3. physical\_attribute\_clause

This clause is used to specify the physical storage properties of the created table or whether to immediately create the segment upon table creation.

##### 9.3.1. TABLESPACE tablespace\_name

This clause specifies the tablespace where the table is located.

For [temporary tables](#temptable), only a temporary type tablespace can be specified. If omitted, the default will be the tablespace of the user to which the table belongs (a default temporary tablespace is specified when the database is created).

For LSC tables, the specified tablespace must have the bucket attribute, see the description of [CREATE TABLESPACE](CREATE TABLESPACE). The default tablespace of the system (DEFAULT tablespace) already has a bucket attribute by default and can serve as the tablespace for LSC tables.

In ISC Distributed Cluster Deployment, this clause cannot specify the tablespace when creating a sharded table.

##### 9.3.2. TABLESPACE SET tablespace\_set\_name

Specifies the set of tablespaces where the sharded table resides. If omitted or specified as `DEFAULT`, it defaults to the user's default tablespace set. If the user has not specified a default tablespace set, the built-in USERS tablespace set will be used.

##### 9.3.3. PCTFREE/PCTUSED/INITRANS/MAXTRANS

Specifies the PCTFREE/PCTUSED/INITRANS/MAXTRANS attributes of the table; if omitted, they default to 8/NULL/2/255.

Where:

- PCTFREE: Represents the percentage of space retained for updating database objects in the data block; when available space falls below this percentage, inserts cannot be performed, only updates can be made.
- PCTUSED: Represents the minimum percentage of space that has been used in the data block for the database object; inserts can be made when the space occupied by data falls below this percentage. 
- INITRANS: Represents the initial number of concurrent transaction entries in each data block.
- MAXTRANS: Represents the maximum number of concurrent transaction entries in each data block.

##### 9.3.4. storage\_clause

Specifies the initial space and maximum space size allocated for the table. Since YashanDB adopts a delayed allocation strategy (space is allocated only when there is data), it is not recommended to specify this parameter.

<span id="deferredsegmentcreation" name="deferredsegmentcreation" class="yaslink"></span>

##### 9.3.5. deferred\_segment\_creation

<span id="segmentcreation" name="segmentcreation" class="yaslink"></span>

###### 9.3.5.1. SEGMENT CREATION DEFERRED|IMMEDIATE

Specifies how segments are created when the table object is created; this syntax does not apply to temporary tables.

- IMMEDIATE: Indicates immediate creation.
- DEFERRED: Indicates delayed creation, which is the default method.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE TABLE employee_info (
name CHAR(10) NOT NULL,
age INT,
id INT NOT NULL
)TABLESPACE users PCTFREE 50 PCTUSED 20 INITRANS 3 MAXTRANS 254 SEGMENT CREATION DEFERRED;
```

<span id="tablepartitionclause" name="tablepartitionclause" class="yaslink"></span>

#### 9.4. table\_partition\_clause

This clause is used to create partitions for tables or composite partitions. Information about all partitions and subpartitions can be viewed through the USER_TAB_PARTITIONS and USER_TAB_SUBPARTITIONS views.

When creating composite partitions, the first-level partition cannot be specified as an INTERVAL-type range partition (range_partitions).

In ISC Distributed Cluster Deployment, this clause can only be used to create partitions for duplicated tables. To create partitions for sharded tables, please use the [consistent_hash_partitions](#consistenthashpartitions) statement.

This clause is subject to the following restrictions:

-  Partitions cannot be created for [temporary tables](#temptable).
- LOB columns/JSON columns cannot be specified as partition keys and subpartition keys.

<span id="rangepartitions" name="rangepartitions" class="yaslink"></span>

##### 9.4.1. range\_partitions

Creates range partitions, and when the partition columns are multiple, they are separated by `,`.

If INTERVAL is specified, the number of partition columns must be 1, and it can only be either numeric or datetime types.

<span id="intervalclause" name="intervalclause" class="yaslink"></span>

###### 9.4.1.1. interval\_clause

Creates INTERVAL-type range partitions.

**INTERVAL(expr)**

Range partitions are defined based on partition boundary values that set a lower limit (the high value of the previous partition) and high value (partition boundary value) for each partition. The high value of the last partition will be the maximum partition boundary value. When the data to be inserted exceeds the maximum partition boundary value, the system will create a new partition according to the following rules:

* A new partition will be automatically named by the system.
* The high value of the new partition will be the maximum partition boundary value plus the INTERVAL value, with the new high value becoming the new maximum partition boundary value.
* If the maximum partition boundary value is defined as MAXVALUE, new partitions cannot be created, and the data will not be inserted into the table.
* The `expr` is an expression, and its resultant type must be consistent with the data type of the partition column.

At this point, if a partition index (Local Partitioned Index) has been established on the table, the system will also create corresponding index partitions when creating a new partition.

**STORE IN**

Specifies tablespaces for INTERVAL partitions, and multiple tablespaces can be specified, separated by `,`. The system will cyclically use the specified tablespaces when creating new partitions. If this statement is not specified, the tablespace of the partition will default to that of the table.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create the range partition table orders_info and specify the INTERVAL value
DROP TABLE IF EXISTS orders_info;
CREATE TABLE orders_info (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
TABLESPACE yashan
PARTITION BY RANGE (id)
INTERVAL (1000)
(PARTITION p_orders_info_1 VALUES LESS THAN (800));
  
-- Insert three records successfully if INTERVAL is not specified, only the first record can be inserted successfully
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',300);
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',2000);
INSERT INTO orders_info VALUES ('20010102020001','product 001','02','0201',SYSDATE-400,'0001',20000);
COMMIT;
  
-- Query partitions on the table; besides the p_orders_info_1 partition, the system automatically created two other partitions which are p_orders_info_2, p_orders_info_3
SELECT TABLE_NAME t_name,TABLESPACE_NAME ts_name,PARTITION_NAME par_name,HIGH_VALUE par_max FROM USER_TAB_PARTITIONS WHERE TABLE_NAME='ORDERS_INFO';
T_NAME             TS_NAME   PAR_NAME         PAR_MAX  
----------------- ---------- ---------------- ---------
ORDERS_INFO       YASHAN     SYS_P22          20800    
ORDERS_INFO       YASHAN     SYS_P21          2800     
ORDERS_INFO       YASHAN     P_ORDERS_INFO_1  800   

-- Create a partition key of datetime type INTERVAL range partition table and specify the STORE IN statement
DROP TABLE IF EXISTS orders_info;
CREATE TABLE orders_info (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY RANGE (order_date)
INTERVAL (INTERVAL '3' month) STORE IN (yashan1,yashan2)
(PARTITION p_firstquarter VALUES LESS THAN (DATE '2000-01-01'));
```

<span id="rangevaluesclause" name="rangevaluesclause" class="yaslink"></span>

###### 9.4.1.2. range\_values\_clause

Defines the high value of the range partitions.

**literal**

When the partition columns are multiple, the high values must correspond to the partition columns, separated by `,`.

**MAXVALUE**

MAXVALUE indicates that there is no upper limit, meaning all data exceeding the upper limit of the previous partition will enter this partition, including NULL values. If all partition columns are defined to have the high value of MAXVALUE partition, no further definition of MAXVALUE partitions can be done.

Once a partition with MAXVALUE is defined, new partitions cannot be added through the ADD PARTITION statement; they can only be split from an existing partition using the SPLIT PARTITION statement.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP TABLE IF EXISTS orders_maxvalue;
CREATE TABLE orders_maxvalue(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY RANGE(id)
(PARTITION p_orders_max_1 VALUES LESS THAN (800),
 PARTITION p_orders_max_2 VALUES LESS THAN (1000),
 PARTITION p_orders_max_3 VALUES LESS THAN (1500),
 PARTITION p_orders_max_4 VALUES LESS THAN (MAXVALUE)
);

-- Create a partition table of multiple partition columns based on ranges
DROP TABLE IF EXISTS orders_multikey;
CREATE TABLE orders_multikey(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY RANGE(id,order_date)
(PARTITION p_orders_max_1 VALUES LESS THAN (800,'2010-01-01'),
 PARTITION p_orders_max_2 VALUES LESS THAN (1000,'2010-05-01'),
 PARTITION p_orders_max_3 VALUES LESS THAN (1500,'2010-10-01')
);

-- After defining partitions with MAXVALUE as high value for all partition columns, if you try to define another partition with MAXVALUE will return an error
DROP TABLE IF EXISTS orders_multimax;
CREATE TABLE orders_multimax(
order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
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

<span id="tablepartitiondescription" name="tablepartitiondescription" class="yaslink"></span>

###### 9.4.1.3. table\_partition\_description

Defines the storage properties of the range partition.

**TABLESPACE tablespace_name**

For the partition specified a tablespace, if omitted, it defaults to the tablespace of the table.

**PCTFREE/PCTUSED/INITRANS/MAXTRANS**

Specifies the PCTFREE/PCTUSED/INITRANS/MAXTRANS attributes; if omitted, they default to 8/NULL/2/255.

Each property's definition is consistent with that of the [physical_attribute_clause](#physicalattributeclause).

**deferred_segment_creation**

**SEGMENT CREATION DEFERRED|IMMEDIATE**

Same as the description in the [physical_attribute_clause](#physicalattributeclause) for SEGMENT CREATION.

***Example*** for Standalone Deployment

```sql
-- Create a range partition with storage properties defined
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

<span id="listpartitions" name="listpartitions" class="yaslink"></span>

##### 9.4.2. list\_partitions

Creates a list partition, and when multiple partition columns are present, they are separated by `,`.

<span id="listvaluesclause" name="listvaluesclause" class="yaslink"></span>

###### 9.4.2.1. list\_values\_clause

Defines the contents of the list partition.

**DEFAULT**

DEFAULT indicates that all data beyond the defined list value should enter this partition. If a DEFAULT partition is not specified, inserting any data outside the defined list values will return an error. If specified, it must be designated as the last partition, and only one DEFAULT partition is allowed.

Once a partition with DEFAULT is defined, new partitions cannot be added through the ADD PARTITION statement; they can only be split from an existing partition using the SPLIT PARTITION statement.

**list_values**

When the partition columns are multiple, they must be specified with `,` and correspond to the partition columns, and the elements of the list must be specified as literal or NULL.

###### 9.4.2.2. table\_partition\_description

Consistent with the description of [range partitions](#tablepartitiondescription).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create a sales information table with list partitions
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

<span id="hashpartitions" name="hashpartitions" class="yaslink"></span>

##### 9.4.3. hash\_partitions

Creates a hash partition where multiple partition columns are separated by `,`. Hash partitions can be created in the following ways:

- By individually specifying each hash partition.
- By using hash scores to batch specify hash partitions.

<span id="individualpartitionclause" name="individualpartitionclause" class="yaslink"></span>

###### 9.4.3.1. individual\_partition\_clause

This statement is used to specify hash partitions individually, defining the name and storage properties for each partition, separated by `,`. 

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

**partition_storage_clause**

Defines the storage properties for hash partitions.

**TABLESPACE tablespace_name**

For specifying the tablespace for a partition, if omitted, it defaults to that of the table. This operation does not apply to ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment

```sql
DROP TABLE IF EXISTS sales_info_hash;
CREATE TABLE sales_info_hash
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY HASH(year)
(PARTITION p_sales_info_hash_1,
 PARTITION p_sales_info_hash_2);
 
-- Specify a tablespace for the partition
DROP TABLE IF EXISTS sales_info_hashpar;
CREATE TABLE sales_info_hashpar
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4))
PARTITION BY HASH(year)
(PARTITION p_sales_info_hash_1 TABLESPACE users,
 PARTITION p_sales_info_hash_2);
```

<span id="hashpartitionsbyquantity" name="hashpartitionsbyquantity" class="yaslink"></span>

###### 9.4.3.2. hash\_partitions\_by\_quantity

This statement is used to specify hash partitions in bulk based on hash scores, and all partition names are generated by the system.

**hash_partition_quantity**

Indicates the number of hash partitions. 

**STORE IN**

The STORE IN clause describes the tablespace for the hash partitions. Multiple tablespaces can be specified, and the number does not need to match the number of partitions.

the system will cyclically assign tablespaces based on the specified order. If this statement is not specified, the system defaults to using the tablespace of the table for all partitions.

***Example*** for Standalone Deployment

```sql
-- Create hash partitions via hash scoring and specify the STORE IN clause
DROP TABLE IF EXISTS sales_info_hash;
CREATE TABLE sales_info_hash
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4),
 product CHAR(5),
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10))
PARTITION BY HASH(year)
PARTITIONS 4
STORE IN (yashan1,yashan2);
```

<span id="compositerangepartitions" name="compositerangepartitions" class="yaslink"></span>

##### 9.4.4. composite\_range\_partitions

Defines composite partitions for range partitions where each range partition can define one of three subpartition types: range, list, or hash.

<span id="subpartitionbyrange" name="subpartitionbyrange" class="yaslink"></span>

###### 9.4.4.1. subpartition\_by\_range

Defines the partition column or subpartition template for range subpartitions.

<span id="rangepartitiondesc" name="rangepartitiondesc" class="yaslink"></span>

###### 9.4.4.2. range\_partition\_desc

Creates range partitions and their subpartitions.

<span id="rangesubpartitiondesc" name="rangesubpartitiondesc" class="yaslink"></span>

###### 9.4.4.3. range\_subpartition\_desc

Specifically defines storage properties for range subpartitions.

**range_values_clause**

Defines the high value for range partitions.

**literal**

When the partition columns are multiple, the high value must be specified with `,` and correspond to the partition columns.

**MAXVALUE**

MAXVALUE indicates no upper limit, meaning all data exceeding the upper limit of the previous partition will enter this partition, including NULL values. If all partition columns are defined to have a MAXVALUE partition, no further definitions of MAXVALUE partitions can be provided.

**deferred_segment_creation**

**SEGMENT CREATION IMMEDIATE|DEFERRED**

Same as the description in [physical_attribute_clause](#physicalattributeclause) for SEGMENT CREATION.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- range-hash composite partition definition 
CREATE TABLE rh_composite(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY HASH(b)
(
PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1, SUBPARTITION sp2),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3, SUBPARTITION sp4)
);
-- range-list composite partition definition 
CREATE TABLE rl_composite(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY LIST(b)
(
PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1 VALUES(10), SUBPARTITION sp2 VALUES(20)),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3 VALUES(10), SUBPARTITION sp4 VALUES(20))
);
-- range-range composite partition definition 
CREATE TABLE rr_composite(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY RANGE(b)
(
PARTITION p1 VALUES LESS than(1) (SUBPARTITION sp1 VALUES LESS than(10), SUBPARTITION sp2 VALUES LESS than(20)),
PARTITION p2 VALUES LESS than(2) (SUBPARTITION sp3 VALUES LESS than(10), SUBPARTITION sp4 VALUES LESS than(20))
);
```

<span id="compositelistpartitions" name="compositelistpartitions" class="yaslink"></span>

##### 9.4.5. composite\_list\_partitions

Defines composite partitions as list partitions, where each list partition can define one of three subpartition types: range, list, or hash.

<span id="subpartitionbylist" name="subpartitionbylist" class="yaslink"></span>

###### 9.4.5.1. subpartition\_by\_list

Defines the partition column or subpartition template for list subpartitions.

<span id="listpartitiondesc" name="listpartitiondesc" class="yaslink"></span>

###### 9.4.5.2. list\_partition\_desc

Creates list partitions and their subpartitions.

<span id="listsubpartitiondesc" name="listsubpartitiondesc" class="yaslink"></span>

###### 9.4.5.3. list\_subpartition\_desc

Specifically defines the storage properties for list subpartitions.

###### 9.4.5.4. list\_values\_clause

Defines contents for the list partition.

**DEFAULT**

DEFAULT indicates that all data not covered by the defined list values will enter this partition. If a DEFAULT partition is not specified, attempting to insert data beyond the defined list values will return an error. If specified, it must be the last partition, and only one DEFAULT partition is allowed.

Once a DEFAULT partition is defined, new partitions cannot be added using the ADD PARTITION statement; they can only be split from an existing partition using the SPLIT PARTITION statement.

**list_values**

When the partition columns are multiple, they must be specified with `,` and must correspond to the fields of the partition columns.

**deferred_segment_creation**

Used to specify the creation manner of segments upon creating the table; this syntax does not apply to temporary tables.

- IMMEDIATE: indicates immediate creation.
- DEFERRED: indicates delayed creation; if omitted, this is the default.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- list-hash composite partition definition 
CREATE TABLE lh_composite(a INT, b VARCHAR(10))
PARTITION BY LIST(a)
SUBPARTITION BY HASH(b)
subpartitions 8
(PARTITION p1 VALUES(10), PARTITION p2 VALUES(DEFAULT));
-- list-list composite partition definition 
CREATE TABLE ll_composite(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY LIST(b)
(
PARTITION p1 VALUES(1) (SUBPARTITION sp1 VALUES(1), SUBPARTITION sp2 VALUES(2)),
PARTITION p2 VALUES(2) (SUBPARTITION sp3 VALUES(1), SUBPARTITION sp4 VALUES(2))
);
-- list-range composite partition definition 
CREATE TABLE lr_composite(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY RANGE(b)
(
PARTITION p1 VALUES(1) (SUBPARTITION sp1 VALUES LESS than(10), SUBPARTITION sp2 VALUES LESS than(20)),
PARTITION p2 VALUES(2) (SUBPARTITION sp3 VALUES LESS than(10), SUBPARTITION sp4 VALUES LESS than(20))
);
```

<span id="compositehashpartitions" name="compositehashpartitions" class="yaslink"></span>

##### 9.4.6. composite\_hash\_partitions

Defines composite partitions as hash partitions where each hash partition can define one of three subpartition types: range, list, or hash.

<span id="subpartitionbyhash" name="subpartitionbyhash" class="yaslink"></span>

###### 9.4.6.1. subpartition\_by\_hash

Defines the partition column or subpartition template for hash subpartitions and also allows specification of the number of subpartitions.

<span id="hashpartitiondesc" name="hashpartitiondesc" class="yaslink"></span>

###### 9.4.6.2. hash\_partition\_desc

Creates hash partitions and the associated subpartitions.

<span id="individualhashsubparts" name="individualhashsubparts" class="yaslink"></span>

###### 9.4.6.3. individual\_hash\_subparts

Defines the tablespace and segment creation properties for a hash subpartition.

**deferred_segment_creation**

Used to specify the manner of segment creation when creating table objects; this syntax does not apply to temporary tables.

- IMMEDIATE: indicates immediate creation.
- DEFERRED: indicates delayed creation; if omitted, this is the default.

<span id="hashsubpartsbyquantity" name="hashsubpartsbyquantity" class="yaslink"></span>

###### 9.4.6.4. hash\_subparts\_by\_quantity

Used to specify hash subpartitions in bulk based on hash scores.

This clause does not apply to ISC Distributed Cluster Deployment.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- hash-hash composite partition definition 
CREATE TABLE hh_composite(a INT, b VARCHAR(10))
PARTITION BY HASH(a)
SUBPARTITION BY HASH(b)
subpartitions 8
(PARTITION p1, PARTITION p2);
-- hash-list composite partition definition 
CREATE TABLE hl_composite(c1 INT, c2 VARCHAR(10))
PARTITION BY HASH(c1)
SUBPARTITION BY LIST(c2)
(
PARTITION p1(SUBPARTITION sp1 VALUES('a')), 
PARTITION p2 (SUBPARTITION sp3 VALUES('d'), SUBPARTITION sp4 VALUES(DEFAULT))
);
-- hash-range composite partition definition 
CREATE TABLE hr_composite(a INT, b INT, c INT,d INT)
PARTITION BY HASH(a)
SUBPARTITION BY RANGE(b,c,d)
(
PARTITION p1 (SUBPARTITION sp1 VALUES LESS than(10,20,30), SUBPARTITION sp2 VALUES LESS than(20,20,30)),
PARTITION p2
);
```

<span id="subpartitiontemplate" name="subpartitiontemplate" class="yaslink"></span>

##### 9.4.7. subpartition\_template

Defines a subpartition template that supports RANGE, LIST, and HASH types.

By executing this statement, the same definition can be applied to partitions collectively, preventing redundant operations. If a certain partition in a composite partition table has no defined subpartition, the subpartition template will be used to define a default subpartition.

When executing this statement, subpartitions must be named; otherwise, an error will be returned. Subpartitions created via the subpartition template will be named as `partition name_subpartition name`, where `partition name_subpartition name` cannot exceed a total length of 64. You can view all subpartition information through the DBA_TAB_SUBPARTITIONS view.

In ISC Distributed Cluster Deployment:

-  For [sharded tables](#shardtable), the number of partitions corresponds to the total number of chunks.
- Second-level partition names will be `partition name_subpartition name`, with a total length not exceeding 64.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Define a range subpartition template
CREATE TABLE hr_composite_template(a INT, b VARCHAR(10))
PARTITION BY HASH(a)
SUBPARTITION BY RANGE(b)
SUBPARTITION template (SUBPARTITION sub1 VALUES LESS than ('a') , SUBPARTITION sub2 VALUES LESS than (MAXVALUE))
(PARTITION p1,PARTITION p2);
-- Define a list subpartition template
CREATE TABLE list_composite(a INT, b VARCHAR(10)) 
PARTITION BY HASH(a) SUBPARTITION BY LIST(b)
SUBPARTITION template(SUBPARTITION sp1 VALUES('a'), SUBPARTITION sp2 VALUES(DEFAULT))
partitions 8;
-- Define a hash subpartition template
CREATE TABLE hash_composite(a INT, b VARCHAR(10))
PARTITION BY HASH(a)
SUBPARTITION BY HASH(b)
SUBPARTITION template (SUBPARTITION sp1 TABLESPACE yashan1, SUBPARTITION sp2 TABLESPACE yashan2)
(PARTITION p1, PARTITION p2);
-- Partially defined subpartitions will use the subpartition template to create the remaining subpartitions
CREATE TABLE hl_partcomposite(c1 INT,c2 INT)
PARTITION BY HASH(c1)
SUBPARTITION BY LIST(c2)
SUBPARTITION template(SUBPARTITION sp9 VALUES(2))
(PARTITION p1 (SUBPARTITION sp11 VALUES(1)),
 PARTITION p2,
 PARTITION p3);
```

<span id="consistenthashpartitions" name="consistenthashpartitions" class="yaslink"></span>

##### 9.4.8. consistent\_hash\_partitions

This clause can only be used to create partitions for [sharded tables](#shardtable), meaning it establishes the data sharding (chunking) rules for sharded tables; sharded tables must only use this clause to create partitions.

###### 9.4.8.1. CONSISTENT

This keyword is optional and indicates that the partitions created for the sharded table must be specified as consistent hash partitions.

###### 9.4.8.2. column

Specifies the column corresponding to the partition key, and if multiple columns are specified, they are separated by `,`. The data sharding rules for the sharded table are to hash the allocations based on these specified partition key columns or combinations of columns.

YashanDB imposes the following constraints on partition keys:

* The columns must meet type requirements and cannot be of LOB, JSON, RAW, ST_GEOMETRY, ROWID, and UROWID types.
* If a primary key column is defined on the table, the partition key must include one or more of the primary key columns.
* If no primary key column is defined, any column can be used as the partition key.

###### 9.4.8.3. individual\_partition\_clause

This clause is used to specify hash partitions individually. The syntax definition is similar to [individual_partition_clauses](#individualpartitionclause) for Standalone Deployment, but partition table space properties cannot be specified; the partition must use the tablespace of the sharded table.

When specifying partitions individually using this method, the number of specified partitions must match the total number of chunks in the current ISC Distributed Cluster Deployment environment; otherwise, an error will be returned.

###### 9.4.8.4. hash\_partitions\_by\_quantity

This statement is used to batch specify hash partitions based on hash scores. The syntax definition is similar to [hash_partitions_by_quantity](#hashpartitionsbyquantity) for Standalone Deployment, but partition table space properties cannot be specified; the partition must use the tablespace of the sharded table.

**hash_partition_quantity**

Indicates the quantity of hash partitions, which can be specified as the AUTO keyword or any value.

If specified as the AUTO keyword, it will automatically generate hash partitions according to the overall chunk quantity, and the number of hash partitions will equal the total number of chunks.

If specified as a numerical value, it must match the total amount of chunks in the current ISC Distributed Cluster Deployment environment; otherwise, it will automatically generate hash partitions based on the chunk quantity.

> **Note**: 
>
> The total number of chunks can be calculated by the database parameter USERS_DATASPACE_SCALE_OUT_FACTOR multiplied by the number of DN groups in the database creation, where the parameter USERS_DATASPACE_SCALE_OUT_FACTOR is configured during installation and cannot be modified afterward, consult the database administrator to obtain this parameter value.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Recreate the sales_info table and require year and month combinations for hash allocation across nodes (taking a total of 21 chunks as an example)
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

-- Specify the number of hash partitions using the AUTO keyword
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

-- Specify the number of hash partitions using a numerical value
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
 
-- Retrieve data from sales_info by partition
SELECT year,month,product,amount FROM sales_info PARTITION (SYS_P1);
YEAR  MONTH PRODUCT        AMOUNT
----- ----- --------- -----------
2000  12    11001             300
2015  03    11001             300
```

<span id="consistenthashwithsubpartitions" name="consistenthashwithsubpartitions" class="yaslink"></span>

##### 9.4.9. consistent\_hash\_with\_subpartitions

This clause is used to create composite hash partitions for [sharded tables](#shardtable); sharded tables can only use this clause to create composite partitions and are limited to ISC Distributed Cluster Deployment.

The syntax of this clause is similar to that of [composite_hash_partitions](#compositehashpartitions) but does not allow specifying the tablespace for partitions.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Rebuild the sales_info table from the previous example, creating a range subpartition for one of the partitions
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

-- Retrieve data from sales_info by composite partition
SELECT year,month,product,quantity,amount
FROM sales_info SUBPARTITION (sp2);
YEAR  MONTH PRODUCT      QUANTITY      AMOUNT
----- ----- --------- ----------- -----------
2000  12    11001              40         300
2015  03    11001              50         300
```

<span id="lobclause" name="lobclause" class="yaslink"></span>

#### 9.5. **lob\_clause**

This statement is used to specify the storage properties of LOB/JSON type column fields, with multiple columns specified separated by commas.

- Parameters like KEEP_DUPLICATES/DEDUPLICATE, CHUNK integer, NOCACHE/CACHE, LOGGING/NOLOGGING, NOCOMPRE/COMPRESS(LOW|MEDIUM|HIGH) are only for syntax compatibility. The tablespace space_name parameter in the partition lob and secondary partition lob lob clause is only for syntax compatibility.
- The integer value range for chunk integer is `(0,32K]`.
- The lob clause for hash partition and secondary partition can only specify the TABLESPACE space_name parameter.

##### 9.5.1. BASICFILE|SECUREFILE

This statement is for syntax compatibility and has no actual meaning.

##### 9.5.2. TABLESPACE

Specifies the tablespace for storing LOB/JSON data out of line. If this attribute is not specified, LOB/JSON data is stored in the same tablespace as that of the table.

###### 9.5.2.1. DISABLE STORAGE IN ROW

Specifies that for LOB/JSON column fields, regardless of their size, out-of-line storage is performed, creating new storage space rather than storing it together with other column field data within the row.

###### 9.5.2.2. ENABLE STORAGE IN ROW

Specifies that the data of LOB/JSON column fields is stored in-line, meaning it is stored together with other column field data in the row. However, when the size of the data exceeds a certain in-line limit, even if ENABLE STORAGE IN ROW is specified, out-of-line storage will still be performed. For HEAP tables, this limit is 4000 bytes; for TAC/LSC tables, this limit is 32000 bytes.

> **Note**: 
>
> When storing data, some internal metadata will be generated. The byte limit for heap tables includes the space occupied by this metadata, while for LSC tables, it does not.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create a table for employee information, where introduce is a long text LOB type field
CREATE TABLE employees1(area CHAR(2),
branch CHAR(4),
employee_info JSON,
introduce CLOB)
LOB(introduce,employee_info) STORE AS (TABLESPACE yashan ENABLE STORAGE IN ROW);
  
-- Insert two rows of data, where the first row's introduce is stored inline due to data length being less than 3988 bytes, while the second row uses out-of-line storage, and the json field length is less than 3988 bytes, stored inline.
INSERT INTO employees1 VALUES ('04','0401',
JSON('{"employee_no":"0401010008","employee_name":"Sam1","sex":"1","entire_date":"SYSDATE-3"}'),
'Shenzhen Institute of Computing Science is one of the top ten basic research institutions approved by the Shenzhen Municipal Government in November 2018');
INSERT INTO employees1 VALUES ('04','0401',
JSON('{"employee_no":"0401010008","employee_name":"Sam2","sex":"1","entire_date":"SYSDATE-3"}'),
LPAD('Shenzhen',4000,'Shenzhen'));
COMMIT;
 
SELECT JSON_FORMAT(employee_info), LENGTHB(introduce) FROM employees1;
JSON_FORMAT(EMPLOYEE                                                LENGTHB(INTRODUCE) 
---------------------------------------------------------------- --------------------- 
{"sex":"1","employee_no":"0401010008","entire_date":"SYSDATE-3","employee_name":"Sam1"}                   108
{"sex":"1","employee_no":"0401010008","entire_date":"SYSDATE-3","employee_name":"Sam2"}                 12000
```

<span id="loggingclause" name="loggingclause" class="yaslink"></span>

#### 9.6. logging\_clause

Used to specify the logging properties of the table, with a default value of logging. For logging description, please refer to the logging_clause section of [ALTER TABLE](ALTER TABLE).

The logging_clause is not applicable to ISC Distributed Cluster Deployment.

<span id="parallelclause" name="parallelclause" class="yaslink"></span>

#### 9.7. parallel\_clause

This statement is used to specify the default parallelism for subsequent queries on the table, which can be omitted; omitting means no parallelism by default.

The tables with the PARALLEL attribute have the following characteristics:

- During actual query operations, the priority of parallelism is as follows:

    - The parallel degree specified via [hint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/hint) has the highest priority.

    - When the configuration parameter [DEGREE_OF_PARALLEL](../../All Manuals/Reference Manual/Configuration Parameters.html#degreeofparallel) is set to 1 (default), the parallel degree attribute of the table takes precedence over DEGREE_OF_PARALLEL.
    
    - When DEGREE_OF_PARALLEL > 1, the priority of DEGREE_OF_PARALLEL is higher than the table's parallel degree attribute.

- When set to PARALLEL 1, it will be treated as if the table's parallel attribute is not set.

- When performing insert into T2 select T1, if both T1 and T2 have parallelism set, T2's insert will also use parallelism.

- Whether to perform a parallel scan is consistent with the limits of hint-specified parallelism.

##### 9.7.1. NOPARALLEL

Setting the parallel attribute to NOPARALLEL indicates no parallelism.

##### 9.7.2. PARALLEL (INTEGER)

Setting the parallel attribute to PARALLEL (INTEGER) indicates parallelism, meaning default parallel scanning is used when performing a full table scan on this table.

**integer**

The parallelism value, with a range of [1, number of server CPU cores * 2]; it can be omitted, in which case the parallelism value defaults to CPU cores * 0.5.

***Example***

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

<span id="cacheclause" name="cacheclause" class="yaslink"></span>

#### 9.8. cache\_clause

For syntax compatibility, has no actual meaning.

<span id="readonlyclause" name="readonlyclause" class="yaslink"></span>

#### 9.9. readonly\_clause

For syntax compatibility, has no actual meaning.

<span id="inmemoryclause" name="inmemoryclause" class="yaslink"></span>

#### 9.10. inmemory\_clause

For syntax compatibility, has no actual meaning.

<span id="tablecompression" name="tablecompression" class="yaslink"></span>

#### 9.11. table\_compression

For syntax compatibility, has no actual meaning.

<span id="nestedtableclause" name="nestedtableclause" class="yaslink"></span>

#### 9.12. nested\_table\_clause

This statement is used to define and create nested tables. In YashanDB, nested tables are used to implement storage for UDT columns (User Defined Types) containing Nested Table types. For more details, please refer to the description of [User UDT] (). This statement is not applicable to YAC/Distributed Cluster Deployment.

##### 9.12.1. nested\_item

Specifies the corresponding column field for the nested table or column field property (which must be of Nested Table type).

##### 9.12.2. COLUMN\_VALUE

When there are multiple levels of nested tables (the column field is a row member containing a collection of Nested Tables), the COLUMN_VALUE keyword is used to represent the nested_item for the inner nested table.

##### 9.12.3. LOCAL|GLOBAL

When the main table is a partition table, the LOCAL keyword is used to specify that the nested table also corresponds to the main table's partition. The GLOBAL keyword specifies that the nested table is not partitioned. If the keyword is omitted, LOCAL is the default.

##### 9.12.4. storage\_table

Specifies the name of the nested table, which cannot be omitted and must comply with YashanDB's [object naming standards](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

For multiple levels of nested tables, the inner nested table information is defined after the name following (nested_table_clause).

##### 9.12.5. TABLESPACE

Specifies a separate tablespace for the nested table. If omitted, it defaults to storing the nested table in the same tablespace as the main table.

***Example*** for Standalone Deployment

```sql
DROP TABLE IF EXISTS area_intro;
DROP TABLE IF EXISTS province_intro;
CREATE OR REPLACE TYPE tb_type AS TABLE OF CHAR(10);
/
CREATE OR REPLACE TYPE obj_type AS OBJECT(city_id INT, branch_list tb_type);
/

-- Create a single-layer nested table 
CREATE TABLE province_intro (id INT, citys tb_type, city_intro obj_type)
NESTED TABLE citys STORE AS nt_province_citys TABLESPACE yashan
NESTED TABLE city_intro.branch_list STORE AS nt_province_branch;

-- Create a multi-layer nested table 
CREATE OR REPLACE TYPE tb_type_city AS TABLE OF tb_type;
/
CREATE TABLE area_intro(id INT, provinces tb_type_city)
NESTED TABLE provinces STORE AS nt_area_provinces(
    NESTED TABLE COLUMN_VALUE STORE AS nt_nt_province_citys);
```

<span id="tableencryptionclause" name="tableencryptionclause" class="yaslink"></span>

#### 9.13. table\_encryption\_clause

This statement is used to specify the encryption properties of the table. This statement can be omitted, and if omitted, the created table is not encrypted.

The rules for table encryption are as follows:

- In both Standalone/YAC/Distributed Cluster Deployment, before creating an encrypted table, key management configuration must be completed, including creating a wallet, opening the wallet, and setting the master key. For specific operations, please refer to [Configuring Wallet](../../All Manuals/Product Security/Encryption/Storage Encryption/Key Management.html#configuringwallet).
- The sys user cannot perform table encryption.
- Once specified, the table encryption attributes cannot be modified.
- If both table encryption and tablespace encryption are used, the table encryption algorithm will take precedence.

**ENCRYPT**

Enables the encryption functionality for the table. Specifying ENCRYPT indicates the creation of an encrypted table.

**USING encryption_algorithm**

This statement is used to specify the encryption algorithm, supporting AES128 and SM4. It can be omitted; if omitted, the default AES128 algorithm is used.

> **Note**:
>
> If you need to use the national encryption algorithm SM4 or TDE related key management functionality, please refer to [Dependencies Preparation](../../All Manuals/Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that tools meeting the requirements are installed on the server system.

***Example***

```sql
-- Create an encrypted table specifying the use of the SM4 encryption algorithm
DROP TABLE IF EXISTS encrypt_area;
CREATE TABLE encrypt_area
(area_no CHAR(2), 
area_name VARCHAR2(60), 
DHQ VARCHAR2(20)) 
ENCRYPT USING 'SM4';

-- Create an encrypted table without specifying an encryption algorithm
DROP TABLE IF EXISTS encrypt_branches;
CREATE TABLE encrypt_branches
(branch_no CHAR(4), 
branch_name VARCHAR2(200), 
area_no CHAR(2), 
address VARCHAR2(200)) 
ENCRYPT;
```

<span id="objectaffinityclause" name="objectaffinityclause" class="yaslink"></span>

#### 9.14. object\_affinity\_clause

In YAC, this statement is used to assign metadata information of table and partition objects to specific database instances. When business accesses object data blocks, it reduces the network overhead of accessing data block metadata information, improving data access efficiency.

- default: Indicates affinity of object resources to the instance currently executing the `CREATE TABLE` statement.
- INSTANCE instance_number: Indicates affinity of object resources to the specified cluster instance.
- auto: Automatically distributes object resources to cluster instances using hash algorithm.

When using the resource affinity feature, the following usage constraints must be followed:

- Currently only supports specifying affinity attributes for tables and table partitions, does not support setting affinity policies for sub-partitions, and sub-partition affinity policies are consistent with first-level partition affinity policies.
- Temporary tables do not support setting affinity attributes.
- Modification of object affinity attributes is not supported.
- When specifying table affinity attributes, indexes, LOBs, and partitions under the corresponding table inherit the table's affinity attributes by default.
- When specifying table partition affinity attributes, local partition indexes and LOB partitions of the corresponding partition inherit the current partition's affinity attributes by default.
- After TRUNCATE TABLE, new SEGMENT objects generated by subsequent data insertion automatically inherit the table's affinity attributes.

***Example*** for YAC Deployment

```sql
-- View cluster instance information
SELECT STATUS,INSTANCE_NUMBER,INSTANCE_NAME FROM GV$INSTANCE WHERE DATABASE_STATUS = 'ACTIVE';

STATUS        INSTANCE_NUMBER INSTANCE_NAME
------------- --------------- ----------------------------------------------------------------
OPEN                        1 yasdb1
OPEN                        2 yasdb2

-- Create table and affinity object to corresponding instance_number
CREATE TABLE test_affinity(a INT) object affinity instance 1;
INSERT INTO test_affinity VALUES(1);
COMMIT;

-- View system resource affinity information through views
SELECT * FROM V$GRC_AFFINITY_POLICY;

       DATA_OBJECT_ID POLICY      MASTER CURRENT_MASTER PREVIOUS_MASTER   STATUS
--------------------- --------- -------- -------------- --------------- --------
                  960 AFFINITY         1              1                        0
                  961 AFFINITY         2              2                        0
                 2919 AFFINITY         1              1                        0

```

<span id="lsctableproperties" name="lsctableproperties" class="yaslink"></span>

### 10. lsc\_table\_properties

This statement is used to define the exclusive properties of LSC tables. These properties can only be specified when creating LSC tables; otherwise, an error will be reported.

#### 10.1. compression\_clause

Defines the compression properties of the LSC table. It describes the same as that for [column field definition compression properties](#compressionclause), and the system compresses data for all columns according to this setting.

<span id="tablesortclause" name="tablesortclause" class="yaslink"></span>

#### 10.2. table\_sort\_clause

This statement is used to define the sort key for LSC tables, where the sort sequence can be any column in the table (can be combined). If this statement is not specified, the first column is defaulted as the sort key, with a default value of NULLS FIRST ASC.

YashanDB will adaptively sort the storage of LSC table data according to certain sorting granularity. The sort key specified by this statement is only used as an acceleration property; when performing operations such as queries, updates, or deletions on the table, if the filtering conditions include the defined sort key, the system will perform sorting acceleration calculations at the storage level for better query performance.

##### 10.2.1. column\_name

This statement is used to specify the sorting sequence of the table. The sorting sequence cannot be of the following types:

- CLOB
- BLOB
- NCLOB
- BIT
- ROWID
- CURSOR
- JSON
- UDT
- RAW

##### 10.2.2. NULLS (FIRST|LAST)

This statement is used to specify the position of null values, where NULLS FIRST indicates null values are arranged at the front, and NULLS LAST indicates null values are arranged at the end. If this statement is not specified, for ascending order, the default is NULLS LAST, and for descending order, the default is NULLS FIRST.

##### 10.2.3. ASC|DESC

This statement is used to specify the sorting method, where ASC indicates ascending order and DESC indicates descending order. If omitted, the default is ascending order.

##### 10.2.4. SCOL

By default, mutable data (MCOL) of LSC tables will also use the sort key. If mutable data (MCOL) does not require point query acceleration, it can be specified at creation time to only use the sort key for stable data (SCOL). The sort key for mutable data (MCOL) can also be enabled or disabled through the [ALTER TABLE](ALTER TABLE) statement.

> **Note**: 
>
> The sort key for mutable data will reduce import performance. For existing data in production environments, it is recommended to import first and then enable the sort key for mutable data or import as stable data using the Bulkload mode.

***Example*** for LSC tables

```sql
-- Create an LSC table with compression and storing sorted by specified columns
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

<span id="mcolttlclause" name="mcolttlclause" class="yaslink"></span>

#### 10.3. mcol\_ttl\_clause

This statement is used to define the maximum lifecycle for mutable data in an LSC table.

In YashanDB, data inserted into LSC tables is first stored as mutable data (MCOL), and under certain conditions, a background thread will be triggered to convert mutable data to stable data (SCOL, non-mutable data) into the data bucket (bucket, object storage directory).

This value guarantees that data inserted by users will be retained in the mutable data region for at least half of the MCOL TTL time, and at most for the entire MCOL TTL time.

After specifying the maximum lifecycle for mutable data, the system will use this value along with the data volume as the condition for triggering the conversion thread, allowing users to set a more reasonable conversion time based on their business needs.

**timestamp**

Lifecycle value, which must follow the format representation of INTERVAL YEAR TO MONTH or INTERVAL DAY TO SECOND data types, such as `'1' YEAR(9)`, `'30:59.9' MINUTE TO SECOND(6)`; for details, please refer to the description of these two types in [Date-Time Types](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/Date-Time Types).

***Example*** for LSC tables

```sql
-- Create an LSC table with a mutable data lifecycle of 1 month; that is, after 1 month, MCOL data will be converted to SCOL data
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

<span id="transformerclause" name="transformerclause" class="yaslink"></span>

#### 10.4. transformer\_clause

LSC tables default have the capability to convert, merge, and generate AC data through background data conversion.

In some cases, if you want to set certain background data conversion capabilities at table creation time, you can use this syntax to turn them off or on. After the table creation is completed, you can also use the [ALTER TABLE](ALTER TABLE) statement to enable or disable the background data conversion capabilities.

***Example*** for LSC tables

```sql
-- Create an LSC table with merge capability disabled
DROP TABLE IF EXISTS lsc_compact_disable;
CREATE TABLE lsc_compact_disable(x INT) DISABLE compact;
```

<span id="mcolabilityclause" name="mcolabilityclause" class="yaslink"></span>

#### 10.5. mcolability\_clause

Specifies whether to enable MCOL at table creation time. If not specified, it depends on the configuration item LSC_MCOL_ENABLED to determine whether to enable MCOL, which is disabled by default.

MCOL is more oriented towards TP business, with good concurrency for small transaction processing capabilities, but is not as good as SCOL in terms of data compression rates and batch import and query performance.

<span id="rowmovementclause" name="rowmovementclause" class="yaslink"></span>

### 11. row\_movement\_clause

This statement is used to specify whether the row data in the created table can be physically moved. For HEAP tables and TAC tables, omitting defaults to DISABLE ROW MOVEMENT. For LSC tables, omitting defaults to ENABLE ROW MOVEMENT.

In YashanDB, the default Rowid of a row does not change; however, in certain special scenarios, such as updating the partition key value of a partition table, causing its row to need to migrate from the current partition to another partition, the Rowid of the row will change. This change is allowed when the table has ENABLE ROW MOVEMENT set, and YashanDB will ensure strong transactional consistency in this case to avoid lost updates.

Enabling row movement may trigger statement restarts in concurrent transactions (rollback previous operations and re-execute DML transactions), affecting concurrent performance. Therefore, it is generally not recommended to enable this switch. In business scenarios where row migrations are predictable (such as flashback data), it can be temporarily opened using the [ALTER TABLE](ALTER TABLE) statement and closed after finishing.

In ISC Distributed Cluster Deployment, enabling ROW MOVEMENT is only necessary during cross-partition updates of partition tables or updates of stable data in LSC tables. In cross-partition update scenarios, only cross-partition updates within DN nodes can be performed, and partition key updates cannot be made.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create a partition table with ENABLE ROW MOVEMENT
DROP TABLE IF EXISTS orders_info_rowmove;
CREATE TABLE orders_info_rowmove (order_no CHAR(14) NOT NULL,
order_desc VARCHAR2(100),
area CHAR(2),
branch CHAR(4),
order_date DATE DEFAULT SYSDATE NOT NULL,
salesperson CHAR(10),
id NUMBER)
PARTITION BY RANGE (id )
(PARTITION p1 VALUES LESS THAN (800),
PARTITION p2 VALUES LESS THAN (1800),
PARTITION p3 VALUES LESS THAN (2800))
ENABLE ROW MOVEMENT;
```
