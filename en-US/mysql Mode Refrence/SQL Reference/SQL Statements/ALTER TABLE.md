## General Description

The ALTER TABLE statement is used to modify the structure, definition, and related properties of a table.

## Statement Definition

**alter table::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name 
(alter_column_clause|alter_table_partition|alter_index_clause|alter_constraint_clause|AUTO_INCREMENT ["="] value)
```

**[alter\_column\_clause](#altercolumnclause)::=**

```ebnf+diagram
syntax::= change_column_clause|modify_column_clause|add_column_clause|rename_column_clause|drop_column_clause
```

**[change_column_clause](#changecolumnclause)::=**

```ebnf+diagram
syntax::= CHANGE [COLUMN] (old_col_name new_col_name column_definition)
```

**[modify_column_clause](#modifycolumnclause)::=**

```ebnf+diagram
syntax::= MODIFY [COLUMN] (col_name column_definition)
```

**[add\_column\_clause](#addcolumnclause)::=**

```ebnf+diagram
syntax::= ADD [COLUMN] (col_name column_definition)
```

**[column\_definition](#conDefdesc)::=**

```ebnf+diagram
syntax::= column dataType [(CHARACTER SET charset_name | COLLATE collation_name | COMMENT "string" | DEFAULT default_expr | inline_constraint | AUTO_INCREMENT) 
{" " (CHARACTER SET charset_name | COLLATE collation_name | COMMENT "string" | DEFAULT default_expr | inline_constraint)}]
```

**rename\_column\_clause::=**

```ebnf+diagram
syntax::= RENAME [COLUMN] old_column_name TO new_column_name
```

**drop\_column\_clause::=**

```ebnf+diagram
syntax::= DROP [COLUMN] "(" (column_name) {"," (column_name)} ")"
```

**[alter_table_partition](#addtablepartition)::=**

```ebnf+diagram
syntax::= add_table_partition
|drop_table_partition
```

**[add\_table\_partition](#addtablepartition)::=**

```ebnf+diagram
syntax::= ADD PARTITION (((PARTITION [partname] add_range_partition_clause)
{"," (PARTITION [partname] add_range_partition_clause)})| 
	((PARTITION [partname] add_list_partition_clause)
    {"," (PARTITION [partname] add_list_partition_clause)})| 
	(PARTITION [partname] add_hash_partition_clause))
```

**[add\_range\_partition\_clause](#addrangepartitionclause)::=**

```ebnf+diagram
syntax::= range_values_clause [partition_storage_clause]
[("(" (
 (individual_hash_subparts  { "," individual_hash_subparts})) ")")]
```

**[range\_values\_clause](#addrangepartitionclause)::=**

```ebnf+diagram
syntax::= VALUES LESS THAN ["("] (literal|MAXVALUE) {"," (literal|MAXVALUE)} [")"]
```

**[add\_list\_partition\_clause](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= list_values_clause [partition_storage_clause]
[individual_hash_subparts { "," individual_hash_subparts}]
```

**[list\_values\_clause](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= VALUES IN "(" (DEFAULT|list_values) ")"
```

**[list\_values](#addlistpartitionclause)::=**

```ebnf+diagram
syntax::= ((literal|NULL) {"," (literal|NULL)})
|(("(" ((literal|NULL) {"," (literal|NULL)})")") {"," ("(" ((literal|NULL) {"," (literal|NULL)})")")})
```

**[add\_hash\_partition\_clause](#addhashpartitionclause)::=**

```ebnf+diagram
syntax::= [partition_storage_clause] "(" partition part_name ")" 
```

**[partition\_storage\_clause](#partitionstorageclause)::=**

```ebnf+diagram
syntax::= ((TABLESPACE tablespace_name) | ([STORAGE] ENGINE ["="] engine_name) | (COMMENT ["="] comment) | (DATA DIRECTORY ["="] data_directory) | (INDEX DIRECTORY ["="] index_directory) | (MAX_ROWS ["="] max_rows) | (MIN_ROWS ["="] min_rows)) 
```

**[drop\_table\_partition](#droptablepartition)::=**

```ebnf+diagram
syntax::= DROP PARTITION ((partname) {"," (partname)}) [update_index_clause]
```

**[update\_index\_clause](#updateindexclause)::=**

```ebnf+diagram
syntax::= [UPDATE | INVALIDATE] GLOBAL INDEXES
```

**[alter\_index\_clause](#alterindexclause)::=**

```ebnf+diagram
syntax::= drop_index_clause|add_index_clause
```

**[add\_index\_clause](#addindexclause)::=**

```ebnf+diagram
syntax::= ADD ((UNIQUE [KEY])|(PRIMARY KEY)|INDEX|KEY) index_name"(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_type]
```
The meanings and usage of index_expr and index_type are introduced in [index_expr](CREATE INDEX.html#indexexpr) and [index_type](CREATE INDEX.html#indextype) in CREATE INDEX.

**[drop\_index\_clause](#dropindexclause)::=**

```ebnf+diagram
syntax::= DROP INDEX index_name
```

**[alter\_constraint\_clause](#alterconstraintclause)::=**

```ebnf+diagram
syntax::= add_constraint_clause|drop_constraint_clause
```

**[add\_constraint\_clause](#addconstraintclause)::=**

```ebnf+diagram
syntax::= ADD CONSTRAINT  constraint_name ((UNIQUE "(" ((column_name) {"," (column_name)}) ")")|(PRIMARY KEY "(" ((column_name) {"," (column_name)}) ")")|(FOREIGN KEY "(" ((column_name) {"," (column_name)}) ")" references_clause)) 
```

**[drop\_constraint\_clause](#dropconstraintclause)::=**

```ebnf+diagram
syntax::= DROP ((PRIMARY KEY)|(FOREIGN KEY forgien_constraint_name)|(CONSTRAINT constraint_name)) 
```

### AUTO\_INCREMENT = value

This statement is used to modify the auto-increment value of a table.

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

### alter\_column\_clause

This statement is used to specify operations on columns of the table.

<span id="changecolumnclause" name="changecolumnclause" class="yaslink"></span>

#### change\_column\_clause

This statement is used to modify attributes such as data type, default value, and constraints of a column. When modifying multiple columns, they should be separated by `,` within `()`.

#### dataType

This modifies the data type of the column field to one recognized by YashanDB [data types](../../../All Manuals/Reference Manual of mysql Mode/SQL Reference/数据类型/数据类型).

The rules for modifying data types of column fields are as follows:

- **Columns with foreign key constraints**: Data types of the corresponding columns in both the child and parent tables may not be modified.
- **Columns with indexes**:
  
    - If the table is empty (i.e., contains no data), it is allowed to modify its type to one other than BLOB/TEXT.

    - For non-empty tables, modifying data types is not allowed.

- **Columns that are partition keys**: Modification of data types is not allowed.

- **Columns not part of foreign keys/indexes/partition keys**:

    - **Columns with original data types of BLOB/TEXT**: Modification of data types is not allowed.
    - **Empty columns with original data types of character types**: It is allowed to modify them to other data types other than BLOB/TEXT.
    - **Non-empty columns with original data types of character types**: Modifications to different character types are allowed, but still must follow these rules:

        - CROSS modification between CHAR/VARCHAR and NCHAR/NVARCHAR is not allowed.

        - When modifying the length property from larger to smaller, it must be ensured that all existing data in this column does not exceed the target data type's limit; otherwise, the modification will not succeed and an error will be prompted.

        - Columns with VARCHAR length less than 8003 are not allowed to have their length increased beyond 8004.

    - **Empty column except for BLOB/TEXT/Character types**: It is allowed to change them to data types that are not BLOB/TEXT.
  
    - **Non-empty column except for BLOB/TEXT/Character types**:

        - Cross-category modification of data types is not allowed, for instance, it is not permitted to change a numeric type to character type.

        - Within the same data category, modifications are only allowed from small to large values, precision, or length attributes. For instance, INT cannot be modified to a NUMBER specified with precision, nor can DATE be modified to TIME.

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### modify\_column\_clause

This statement is used to modify attributes such as data type, default value, and constraints of a column. When modifying multiple columns, they should be separated by `,` within `()`.

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### add\_column\_clause

This statement is used to add columns to the table. When adding multiple columns, they should be separated by `,` within `()`. The COLUMN keyword is optional.

<span id="conDefdesc" name="conDefdesc" class="yaslink"></span>

#### column\_definition



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


<span id="referencesclause" name="referencesclause" class="yaslink"></span>

##### references\_clause

When defining a foreign key constraint, REFERENCES specifies the parent table that the foreign key points to, as well as the corresponding column fields in the parent table that match the foreign key column in the child table. If only the parent table name is specified and the column name is omitted, the foreign key will automatically reference the primary key of the parent table, and the foreign key column must correspond one-to-one with the primary key column.

<span id="addtablepartition" name="addtablepartition" class="yaslink"></span>

### alter\_table\_partition

#### add\_table\_partition

This statement is used to add a new partition to the table.

In mysql mode, range, list, and hash partitioning types are supported.

When creating composite partitions, the first-level partitions only support RANGE and LIST partitions, and the subpartition types can only be HASH and KEY.

<span id="addrangepartitionclause" name="addrangepartitionclause" class="yaslink"></span>

##### add\_range\_partition\_clause

Add a range type partition, which can only be established above the current maximum partition boundary. If the maximum partition boundary of the table is set to MAXVALUE, adding a partition is not allowed.

You can specify the hash subpartitions for the newly added partition, and the type of subpartition should match the table's definition.

If the table was not specified as a composite partition table during creation, you cannot specify subpartitions for the newly added partition using this statement.

<span id="addlistpartitionclause" name="addlistpartitionclause" class="yaslink"></span>

##### add\_list\_partition\_clause

Add a list type partition. If the list values have already been defined with DEFAULT in the table, adding a partition is not allowed.

The partition list values can be specified as a general expression ([expr](../General SQL Syntax/expr)).

You can specify the hash subpartitions for the newly added partition, and the type of subpartition should match the table's definition.

If the table was not specified as a composite partition table during creation, you cannot specify subpartitions for the newly added partition using this statement.

When the partition column count is one and the newly added partition contains multiple values, each value needs to be wrapped in parentheses; otherwise, an error will occur.

<span id="addhashpartitionclause" name="addhashpartitionclause" class="yaslink"></span>

##### add\_hash\_partition\_clause

Add a hash type partition. 

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

##### partition\_storage\_clause

You can specify the tablespace for the newly added partition (defaulting to the tablespace of the table).

The following storage attributes are for syntax compatibility only and do not have actual meanings.

- STORAGE ENGINE
- COMMENT
- DATA DIRECTORY
- INDEX DIRECTORY
- MAX_ROWS
- MIN_ROWS

***Example*** for HEAP Table

```sql
-- Add a partition to a range partition table
ALTER TABLE sales_info_range ADD PARTITION (PARTITION p_sales_info_range_4 VALUES LESS THAN('2038'));

-- Create a range-key composite partition table range_key_table
CREATE TABLE range_key_table(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY key(b)
SUBPARTITIONS 2
(PARTITION p1 VALUES LESS than(1),
PARTITION p2 VALUES LESS than(2));

-- Add a range partition to it 
ALTER TABLE range_key_table ADD PARTITION (PARTITION p3 VALUES LESS THAN(3));

-- Add a partition to a list partition table
ALTER TABLE sales_info_list ADD PARTITION (PARTITION p_sales_info_list_3 VALUES IN ('2022'));
ALTER TABLE sales_info_list ADD PARTITION (PARTITION p_sales_info_list_4 VALUES IN (TO_CHAR(2023)));

-- Create a list-hash composite table list_hash_table
CREATE TABLE list_hash_table(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY HASH(b)
SUBPARTITIONS 2
(PARTITION p1 VALUES IN (1),
PARTITION p2 VALUES IN (2));

-- Add a list partition to it and specify its list subpartition
ALTER TABLE list_hash_table ADD PARTITION (PARTITION p3 VALUES IN (3));

-- Add a partition to a hash partition table
ALTER TABLE sales_info_hash ADD PARTITION (PARTITION p_sales_info_hash_3);
```

<span id="droptablepartition" name="droptablepartition" class="yaslink"></span>

#### drop\_table\_partition

This statement is used to delete the partition of a table, deleting all data within the partition. Specify multiple partitions with commas.

When a partition specified for deletion contains data, the global indexes on the table will become invalid. In ISC Distributed Cluster Deployment mode, regardless of whether the specified partition contains data, the global indexes on the table will become invalid.

The following constraints apply to this statement:

- When deleting partitions, it is not permitted to delete all partitions on the table.
- Hash type partitions cannot be deleted.
- When a partition on the table is deleted, the corresponding index partition is also deleted.
- For range type partitions, after deleting a partition, the boundary of that partition will merge with the neighboring larger partition. Hence, any new table data that falls within the boundary range of the deleted partition will be inserted into the neighboring partition. If there is no neighboring larger partition, the data insertion will not be successful.

<span id="updateindexclause" name="updateindexclause" class="yaslink"></span>

##### update\_index\_clause

Specifies how to handle global indexes (Global Index) when a partition is deleted, defaulting to INVALIDATE.

*   INVALIDATE GLOBAL INDEXES: All global indexes will be invalidated and become unavailable.
*   UPDATE GLOBAL INDEXES: The global indexes remain valid and available.

When using `update_index_clause` SQL statement, the syntax parsing and execution will be performed in accordance with yashan mode, and the database can still provide services normally.

***Example*** for HEAP Table

```sql
-- sales_info_range is a range partition table
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
year  month branch product      quantity      amount salsperson  
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
year  month branch product      quantity      amount salsperson  
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0201   11001              30         500 0201010011 
2000  12    0102   11001              20         300            
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600 
   
INSERT INTO sales_info_range VALUES ('2015','03','0101','11001',20,300,'');
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range PARTITION(p_sales_info_range_3);
year  month branch product      quantity      amount salsperson  
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0101   11001              20         300            
2021  05    0101   11001              40         600            
2015  03    0101   11001              20         300

ALTER TABLE sales_info_list DROP PARTITION p_sales_info_list_4 UPDATE GLOBAL INDEXES;
```

<span id="alterindexclause" name="alterindexclause" class="yaslink"></span>

### alter\_index\_clause

This statement is used to add or drop indexes for the table.

<span id="addindexclause" name="addindexclause" class="yaslink"></span>

#### add\_index\_clause

This statement is used to add indexes to the table. Multiple index columns are separated by `,` within `()`. The index option USING BTREE can be specified.

<span id="dropindexclause" name="dropindexclause" class="yaslink"></span>

#### drop\_index\_clause

This statement is used to drop indexes from the table.

<span id="alterconstraintclause" name="alterconstraintclause" class="yaslink"></span>

### alter\_constraint\_clause

This statement is used to add or drop constraints for the table.

<span id="addconstraintclause" name="addconstraintclause" class="yaslink"></span>

#### add\_constraint\_clause

This statement is used to add constraints to the table.

<span id="dropconstraintclause" name="dropconstraintclause" class="yaslink"></span>

#### drop\_constraint\_clause

This statement is used to drop constraints from the table.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE orders_info (
    id INT,
    num INT,
    total INT
);
ALTER TABLE orders_info MODIFY total DOUBLE;
ALTER TABLE orders_info CHANGE num num DEFAULT 10;
ALTER TABLE orders_info ADD UNIQUE INDEX order_uk(id);
ALTER TABLE orders_info DROP INDEX ORDER_UK;
ALTER TABLE orders_info ADD CONSTRAINT order_pk PRIMARY key(id);
ALTER TABLE orders_info DROP PRIMARY key;
```
