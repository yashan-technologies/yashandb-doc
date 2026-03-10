## General Description

The CREATE TABLE statement is used to create a table object. Currently, only Standalone Deployment HEAP tables are supported.

When creating a table, you can use the COLLATE keyword to specify the [collation](../Basic SQL Elements/Collation) attribute for the table or columns within it. Columns with collation attributes are referred to as collation sequences.

- If the character set collation of the database is not explicitly specified during creation, it will take the value from the collation_server parameter.
- If the character set collation of the table is not explicitly specified during creation, the table will use the character set collation of the database it belongs to.

- If the character set collation of the column is not explicitly specified during creation, the column will use the character set collation of the table it belongs to.

## Statement Definition

**create table::=**

```ebnf+diagram
syntax::= CREATE TABLE [IF NOT EXISTS] [schema "."] table_name "("  relation_properties ")" [table_options] [partition_options]
```

**[relation\_properties](#relationproperties)::=**

```ebnf+diagram
syntax::= (column_definition) {"," (column_definition)|(index_definition)}
```

**[column\_definition](#columndefinition)::=**


```ebnf+diagram
syntax::= column dataType [(CHARACTER SET charset_name | COLLATE collation_name | COMMENT "string" | DEFAULT default_expr | inline_constraint | AUTO_INCREMENT) 
{" " (CHARACTER SET charset_name | COLLATE collation_name | COMMENT "string" | DEFAULT default_expr | inline_constraint)}]
```



**[index\_definition](#indexdefinition)::=**

```ebnf+diagram
syntax::= (INDEX | KEY) [index_name] [USING (BTREE | HASH)] "(" column_name { "," column_name}")" [KEY_BLOCK_SIZE ["="] value] 

syntax::= (INDEX | KEY) [index_name] [KEY_BLOCK_SIZE ["="] value] "(" column_name { "," column_name}")" [USING (BTREE | HASH)] 
```

**[table\_options](#tableoptions)::=**

```ebnf+diagram
syntax::=(ENGINE "=" engine_name  | [DEFAULT] CHARACTER SET ["="] charset_name | [DEFAULT] COLLATE ["="] collation_name| ROW_FORMAT ["="] {DEFAULT | DYNAMIC | FIXED | COMPRESSED | REDUNDANT | COMPACT} | AUTO_INCREMENT ["="] increment_start_value | (MAX_ROWS ["="] max_rows) | (MIN_ROWS ["="] min_rows)) 
```

**[partition\_options](#partitionoptions)::=**

```ebnf+diagram
syntax::= (range_partitions
|list_partitions
|hash_partitions
|range_columns_partitions
|list_columns_partitions
|linear_hash_partitions
|linear_key_partitions
|composite_range_partitions
|composite_list_partitions)
```

**[range\_partitions](#rangepartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY RANGE [COLUMNS] "(" ((column) {"," (column)}) ")" 
"(" (PARTITION [partname]  range_values_clause  [partition_storage_clause])
{"," (PARTITION [partname]  range_values_clause  [partition_storage_clause])} ")"
```

**[range\_values\_clause](#rangevaluesclause)::=**

```ebnf+diagram
syntax::= VALUES LESS THAN ["("] (literal|MAXVALUE) {"," (literal|MAXVALUE)} [")"]
```

**[partition\_storage\_clause](#partitionstorageclause)::=**

The configuration syntax for the storage attributes of table partitions in composite partitioning is consistent with that of table storage attributes.

**[list\_partitions](#listpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY [COLUMNS] LIST "(" (column) {"," (column)} ")" "(" (PARTITION [partname] 
list_values_clause  [partition_storage_clause])
{"," (PARTITION [partname]  list_values_clause  [partition_storage_clause])} ")"
```

**[list\_values\_clause](#listvaluesclause)::=**

```ebnf+diagram
syntax::= VALUES IN "(" (DEFAULT|list_values) ")"
```

**[list\_values](#listvalues)::=**

```ebnf+diagram
syntax::= ((literal|NULL) {"," (literal|NULL)})
|(("(" ((literal|NULL) {"," (literal|NULL)})")") {"," ("(" ((literal|NULL) {"," (literal|NULL)})")")})
```

**[hash\_partitions](#hashpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY [LINEAR] HASH "(" ((column) {"," (column)}) ")"
(individual_partition_clause | hash_partitions_by_quantity)
```

**[individual\_partition\_clause](#individualpartitionclause)::=**

```ebnf+diagram
syntax::= "(" ( PARTITION [partname]  [partition_storage_clause] )
{"," ( PARTITION [partname]  [partition_storage_clause] )}")"
```
**[hash\_partitions\_by\_quantity](#hashpartitionsbyquantity)::=**

```ebnf+diagram
syntax::= PARTITIONS hash_partition_quantity [STORE IN "(" ((tablespace_name) {"," (tablespace_name)}) ")"] 
[OVERFLOW STORE IN "(" ((tablespace_name) {"," (tablespace_name)}) ")"]
```

**[linear\_key\_partitions](#linearkeypartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY LINEAR KEY "(" ((column) {"," (column)}) ")"
(individual_partition_clause | hash_partitions_by_quantity)
```

**[composite\_range\_partitions](#compositerangepartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY RANGE "(" ((column) {"," (column)}) ")"
(subpartition_by_hash|subpartition_by_key)
```
**[subpartition\_by\_hash](#subpartitionbyhash)::=**

```ebnf+diagram
syntax::= SUBPARTITION BY HASH "(" ((column) {"," (column)}) ")" 
[hash_subparts_by_quantity]
```

**[hash_subparts_by_quantity](#hashsubpartsbyquantity)::=**

```ebnf+diagram
syntax::= SUBPARTITIONS integer [subpartition_storage_clause]
```

**[subpartition\_storage\_clause](#partitionstorageclause)::=**

The syntax for the storage attributes of subpartitions in composite partitioning is consistent with that of the table storage attribute table_options.

**[subpartition\_by\_key](#subpartitionbykey)::=**

```ebnf+diagram
syntax::= SUBPARTITION BY KEY algorithm "=" ("1"|"2") "(" ((column) {"," (column)}) ")" 
[hash_subparts_by_quantity]
```

**[composite\_list\_partitions](#compositelistpartitions)::=**

```ebnf+diagram
syntax::= PARTITION BY RANGE "(" ((column) {"," (column)}) ")"
(subpartition_by_hash|subpartition_by_key)
```

<span id="relationproperties" name="relationproperties" class="yaslink"></span>

### relation\_properties

This statement is used to specify the structure of the created table, which includes column fields (Column) and constraints ([Constraint](../General SQL Syntax/constraint)).

<span id="columndefinition" name="columndefinition" class="yaslink"></span>

### column\_definition



This statement is used to define the columns of the table, including data types (DataType), default values (DEFAULT), inline constraints ([inline_constraint](../General SQL Syntax/constraint), and various column attributes such as CHARACTER SET, COLLATE, COMMENT, and AUTO_INCREMENT.

**CHARACTER SET and COLLATE**

In YashanDB (mysql mode), defining the character set for columns is supported. However, during actual configuration, the character set defined for a column must be compatible with the database server's character set; otherwise, the CREATE TABLE statement will fail.

|Database CHARACTER SET | Column CHARACTER SET | COLLATE                                                      | DEFAULT COLLATION   |
|-----| ------------- | ------------------------------------------------------------ | ------------------- |
|ASCII| ASCII         | ASCII_BIN<br>ASCII_GENERAL_CI<br>ASCII_TOLOWER_CI         | ASCII_GENERAL_CI    |
|GBK| GBK           | GBK_BIN<br>GBK_CHINESE_CI<br>GBK_TOLOWER_CI               | GBK_CHINESE_CI      |
|UTF8| UTF8          | UTF8_BIN<br>UTF8_GENERAL_CI<br>UTF8_TOLOWER_CI            | UTF8_GENERAL_CI     |
|UTF8| UTF8MB4       | UTF8_BIN<br>UTF8_GENERAL_CI       | UTF8_GENERAL_CI     |
|UTF8| UTF8MB3       | UTF8_BIN<br>UTF8_GENERAL_CI         | UTF8_GENERAL_CI     |
|ISO88591| LATIN1        | ISO88591_BIN<br>ISO88591_TOLOWER_CI<br>ISO88591_GENERAL_CI     | ISO88591_GENERAL_CI |
|GB18030| GB18030       | GB18030_BIN<br>GB18030_TOLOWER_CI<br>GB18030_CHINESE_CI   | GB18030_CHINESE_CI  |

**AUTO_INCREMENT**

An auto-increment column can be used to generate a unique identifier for new records. A table can only have one auto-increment column, and the auto-increment column must be the first column in an index or composite index. The rules for auto-increment are as follows:

- If no value is assigned to the auto-increment field, the database will automatically populate it with the next auto-increment value (default starts from 1).
- If 0 or null is assigned to the auto-increment field, the database will automatically populate it with the next auto-increment value.

- If a value greater than AUTO_INCREMENT is used, the database will assign that value and set AUTO_INCREMENT = value + 1.
- If a value less than AUTO_INCREMENT but not conflicting is used, data can be inserted, but AUTO_INCREMENT remains unchanged.

- Converting a non-auto-increment column to an auto-increment column is currently not supported.
- To reset the starting value of the auto-increment column, the ALTER TABLE statement can be used.

<span id="indexdefinition" name="indexdefinition" class="yaslink"></span>

### index\_definition

This statement is used to define the index on the table.

- Support BTree and HASH indexes is used for compatibility and have no practical meaning;
- KEY_BLOCK_SIZE is used for compatibility and have no practical meaning.

<span id="tableoptions" name="tableoptions" class="yaslink"></span>

### table\_options

This statement is used to define table options, including ENGINE, AUTO_INCREMENT, CHARACTER SET, COLLATE, and ROW_FORMAT.

Currently, the functional configuration of TABLESPACE, CHARACTER SET, COLLATE, and AUTO_INCREMENT has been completed; other configuration items only provide syntax compatibility.

***Example*** for Standalone Deployment Heap tables

```sql
-- Select Database
USE sales;

-- Table options
CREATE TABLE finance_info(
    id INT auto_increment PRIMARY key,
    name VARCHAR(100) NOT null
) engine = InnoDB CHARACTER SET utf8mb4 collate utf8mb4_bin row_format = DEFAULT AUTO_INCREMENT=100;

-- Column attributes
CREATE TABLE employee_info(
    id INT auto_increment PRIMARY key COMMENT 'primary key',
    name VARCHAR(100) NOT null CHARACTER SET utf8mb4 collate utf8mb4_bin
);

-- With index
CREATE TABLE branches(
    ind INT,
    name VARCHAR(10),
    key(ind)
);
```

<span id="partitionoptions" name="partitionoptions" class="yaslink"></span>

### partition\_options

This statement is used to create partitions (Partition) or composite partitions for the table. All partition and subpartition information can be viewed through the INFORMATION_SCHEMA.PARTITIONS view.

When creating composite partitions in mysql mode, the first-level partitions only support RANGE and LIST partitions, and the subpartition types can only be HASH and KEY.

When creating composite partition in mysql mode with the first-level partition being a HASH partition, the syntax parsing and execution will be performed in accordance with yashan mode, and the database can still provide services normally.

<span id="rangepartitions" name="rangepartitions" class="yaslink"></span>

#### range\_partitions

Create range partitions, with partition columns separated by a comma.

<span id="rangevaluesclause" name="rangevaluesclause" class="yaslink"></span>

##### range\_values\_clause

Defines the high values for range partitions.

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

##### partition\_storage\_clause

Storage properties of the partition. This includes specifying a tablespace for the partition; if omitted, the default tablespace of the table will be used.

The configuration syntax for the storage attributes of table partitions and subpartitions in composite partitioning is consistent with that of table storage attributes.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE orders_info1 (
    id INT,
    num INT
)
PARTITION BY RANGE (id)  (
    PARTITION p0 VALUES LESS THAN (5),
    PARTITION p1 VALUES LESS THAN (MAXVALUE)
);

-- Create a partition table and specify storage properties for both the table and the partitions
CREATE TABLE part_storage(a INT, b CHAR(255)) ENGINE=InnoDB
PARTITION BY RANGE(a, b)
(
	PARTITION p1 VALUES LESS THAN(1, 'a') STORAGE ENGINE=InnoDB,
	PARTITION p2 VALUES LESS THAN(10, 'c') STORAGE ENGINE=InnoDB,
	PARTITION p3 VALUES LESS THAN(MAXVALUE, MAXVALUE) STORAGE ENGINE=InnoDB
); 

-- Create an index and specify storage properties for the index
CREATE INDEX idx_part_storage_2 
ON part_storage(b) 
STORAGE (MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0);
```

<span id="listpartitions" name="listpartitions" class="yaslink"></span>

#### list\_partitions

Create list partitions, with partition columns separated by a comma.

<span id="listvaluesclause" name="listvaluesclause" class="yaslink"></span>

##### list\_values\_clause

Defines the contents of the list partition.

<span id="listvalues" name="listvalues" class="yaslink"></span>

**list_values**

When there are multiple partition columns, they must be separated by commas and correspond to the partition columns, with the contents specified as literal or NULL.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY LIST(store_id) (
    PARTITION pNorth VALUES IN (3,5,6,9,17),
    PARTITION pEast VALUES IN (1,2,10,11,19,20),
    PARTITION pWest VALUES IN (4,12,13,14,18),
    PARTITION pCentral VALUES IN (7,8,15,16)
);
```

<span id="hashpartitions" name="hashpartitions" class="yaslink"></span>

#### hash\_partitions

Create hash partitions, with multiple partition columns separated by a comma. Hash partitions can be created in two ways:

- Individually specifying hash partitions.
- Using hash quantities to specify multiple hash partitions.

<span id="individualpartitionclause" name="individualpartitionclause" class="yaslink"></span>

##### individual\_partition\_clause

This statement is used to individually specify hash partitions, defining each partition's name and storage properties, with partitions separated by commas.

<span id="hashpartitionsbyquantity" name="hashpartitionsbyquantity" class="yaslink"></span>

##### hash\_partitions\_by\_quantity

This statement is used to specify hash partitions in bulk by hash quantity, where all partition names are generated by the system.

**hash_partition_quantity**

Indicates the number of hash partitions.

**STORE IN**

The STORE IN clause describes the tablespace to which the hash partition belongs. Multiple tablespaces can be specified, and the number does not need to match the number of partitions; the system will cycle through the specified tablespaces in the order given.

If not specified, the system uses the tablespace of the table as the tablespace for all partitions by default.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE sales_info (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY HASH(store_id)
PARTITIONS 4;

CREATE TABLE subpart_hash(c1 INT, c2 INT)
  PARTITION BY HASH(c1)
  SUBPARTITION BY HASH(c1)
  SUBPARTITIONS 2
  STORE IN (users)
  (PARTITION p1, PARTITION p2);
```

<span id="rangecolumnspartitions" name="rangecolumnspartitions" class="yaslink"></span>

#### range\_columns\_partitions

Partitions are based on the range of values of one or more columns, where the partitioning criterion is based on the tuples formed by these columns' values.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE sales_info_range (
    quantity INT,
    amount INT
)
PARTITION BY RANGE COLUMNS(quantity, amount) (
    PARTITION p0 VALUES LESS THAN (5, 12),
    PARTITION p3 VALUES LESS THAN (MAXVALUE, MAXVALUE)
);
```

<span id="listcolumnspartitions" name="listcolumnspartitions" class="yaslink"></span>

#### list\_columns\_partitions

Partitions are based on the list of values of one or more columns, where the partitioning criterion is based on the tuples formed by these columns' values.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE city_info (
    first_name VARCHAR(25),
    last_name VARCHAR(25),
    street_1 VARCHAR(30),
    street_2 VARCHAR(30),
    city VARCHAR(15),
    renewal DATE
)
PARTITION BY LIST COLUMNS(city) (
    PARTITION pRegion_1 VALUES IN('Oskarshamn', 'Högsby', 'Mönsterås'),
    PARTITION pRegion_2 VALUES IN('Vimmerby', 'Hultsfred', 'Västervik'),
    PARTITION pRegion_3 VALUES IN('Nässjö', 'Eksjö', 'Vetlanda'),
    PARTITION pRegion_4 VALUES IN('Uppvidinge', 'Alvesta', 'Växjo')
);
```

<span id="linearhashpartitions" name="linearhashpartitions" class="yaslink"></span>

#### linear\_hash\_partitions

Partitions are determined based on linear hashing algorithms, suitable for scenarios that require rapid partition management and relatively uniform data distribution.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE sales_info_hash (
    id INT, 
    name CHAR(5), 
    saltime DATE
)
PARTITION BY LINEAR HASH(saltime)
PARTITIONS 6;
```

<span id="linearkeypartitions" name="linearkeypartitions" class="yaslink"></span>

#### linear\_key\_partitions

Partitions are determined based on linear hashing algorithms, suitable for scenarios that require rapid partition management.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE sales_info_hash1 (
    id INT NOT NULL,
    name CHAR(5),
    altime DATE
)
PARTITION BY LINEAR KEY (id)
PARTITIONS 3;
```

<span id="compositerangepartitions" name="compositerangepartitions" class="yaslink"></span>

#### composite\_range\_partitions

Defines a composite partition for range partitions, where each range partition can define hash or key subpartitions.

<span id="subpartitionbyhash" name="subpartitionbyhash" class="yaslink"></span>

##### subpartition\_by\_hash

Defines the partition columns for hash subpartitions, and it can also specify the number of subpartitions.

<span id="hashsubpartsbyquantity" name="hashsubpartsbyquantity" class="yaslink"></span>

##### hash\_subparts\_by\_quantity

Defines hash subpartitions in bulk using hash scores.

<span id="subpartitionbykey" name="subpartitionbykey" class="yaslink"></span>

##### subpartition\_by\_key

Defines the partition columns for key subpartitions, and it can also specify the number of subpartitions.

**algorithm**

Used to specify the hash algorithm, which is currently used for compatibility and has no actual meaning.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE employees1 (
id INT NOT NULL,
fname VARCHAR(30),
lname VARCHAR(30)
)
PARTITION BY RANGE (id)
SUBPARTITION BY KEY algorithm = 1 (lname)
SUBPARTITIONS 4 (
PARTITION p0 VALUES LESS THAN (25),
PARTITION p1 VALUES LESS THAN (50),
PARTITION p2 VALUES LESS THAN (100),
PARTITION p3 VALUES LESS THAN (MAXVALUE)
);
```

<span id="compositelistpartitions" name="compositelistpartitions" class="yaslink"></span>

#### composite\_list\_partitions

Defines a composite partition for list partitions, where each list partition can define hash or key subpartitions.
