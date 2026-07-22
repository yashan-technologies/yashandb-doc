## 通用描述

CREATE TABLE语句用于创建一个表对象。当前只支持单机HEAP表。

创建表时，可以使用关键字COLLATE为表或表中的列字段指定[字符集排序](../基本SQL元素/字符序)属性，具备字符集排序属性的列称为字符集排序列。

- 如果创建数据库时未显式指定库的字符序，则库的字符序采用collation_server参数的值。
- 如果创建数据表时未显式指定表的字符序，则该表使用所在数据库的字符序。

- 如果创建数据表时未显式指定列字段的字符序，则该字段使用所在表的字符序。

## 语句定义

**create table::=**

```ebnf
= CREATE TABLE [IF NOT EXISTS] [schema "."] table_name "("  relation_properties ")" [table_options] [partition_options].
```

**[relation\_properties](#relationproperties)::=**

```ebnf
= (column_definition) {"," (column_definition)|(index_definition)}.
```

**[column\_definition](#columndefinition)::=**


```ebnf
= column dataType [ZEROFILL] [(CHARACTER SET charset_name | COLLATE collation_name | BINARY | COMMENT 'string' | DEFAULT default_expr | ON UPDATE CURRENT_TIMESTAMP | inline_constraint | AUTO_INCREMENT) 
{" " (CHARACTER SET charset_name | COLLATE collation_name | BINARY | COMMENT 'string' | DEFAULT default_expr | ON UPDATE CURRENT_TIMESTAMP | inline_constraint)}] .
```



**[index\_definition](#indexdefinition)::=**

```ebnf
= (INDEX | KEY) [index_name] [USING (BTREE | HASH)] "(" column_name { "," column_name}")" [KEY_BLOCK_SIZE ["="] value] .

= (INDEX | KEY) [index_name] [KEY_BLOCK_SIZE ["="] value] "(" column_name { "," column_name}")" [USING (BTREE | HASH)] .
```

**[table\_options](#tableoptions)::=**

```ebnf
=(ENGINE "=" engine_name  | [DEFAULT] CHARACTER SET ["="] charset_name | [DEFAULT] COLLATE ["="] collation_name| ROW_FORMAT ["="] {DEFAULT | DYNAMIC | FIXED | COMPRESSED | REDUNDANT | COMPACT} | AUTO_INCREMENT ["="] increment_start_value | (MAX_ROWS ["="] max_rows) | (MIN_ROWS ["="] min_rows)) .
```

**[partition\_options](#partitionoptions)::=**

```ebnf
= (range_partitions
|list_partitions
|hash_partitions
|range_columns_partitions
|list_columns_partitions
|linear_hash_partitions
|linear_key_partitions
|composite_range_partitions
|composite_list_partitions).
```

**[range\_partitions](#rangepartitions)::=**

```ebnf
= PARTITION BY RANGE [COLUMNS] "(" (column {"," column}) ")" 
"(" (PARTITION [partname]  range_values_clause  [partition_storage_clause])
{"," (PARTITION [partname]  range_values_clause  [partition_storage_clause])} ")".
```

**[range\_values\_clause](#rangevaluesclause)::=**

```ebnf
= VALUES LESS THAN ["("] (literal|MAXVALUE) {"," (literal|MAXVALUE)} [")"].
```

**[partition\_storage\_clause](#partitionstorageclause)::=**

表分区存储属性语法与表存储属性table_options语法一致。

**[list\_partitions](#listpartitions)::=**

```ebnf
= PARTITION BY [COLUMNS] LIST "(" column {"," column} ")" "(" (PARTITION [partname] 
list_values_clause  [partition_storage_clause])
{"," (PARTITION [partname]  list_values_clause  [partition_storage_clause])} ")".
```

**[list\_values\_clause](#listvaluesclause)::=**

```ebnf
= VALUES IN "(" (DEFAULT|list_values) ")".
```

**[list\_values](#listvalues)::=**

```ebnf
= ((literal|NULL) {"," (literal|NULL)})
|("(" ((literal|NULL) {"," (literal|NULL)})")" {"," "(" ((literal|NULL) {"," (literal|NULL)})")"}).
```

**[hash\_partitions](#hashpartitions)::=**

```ebnf
= PARTITION BY [LINEAR] HASH "(" (column {"," column}) ")"
(individual_partition_clause | hash_partitions_by_quantity).
```

**[individual\_partition\_clause](#individualpartitionclause)::=**

```ebnf
= "(" ( PARTITION [partname]  [partition_storage_clause] )
{"," ( PARTITION [partname]  [partition_storage_clause] )}")".
```
**[hash\_partitions\_by\_quantity](#hashpartitionsbyquantity)::=**

```ebnf
= PARTITIONS hash_partition_quantity [STORE IN "(" (tablespace_name {"," tablespace_name}) ")"] 
[OVERFLOW STORE IN "(" (tablespace_name {"," tablespace_name}) ")"].
```

**[linear\_key\_partitions](#linearkeypartitions)::=**

```ebnf
= PARTITION BY LINEAR KEY "(" (column {"," column}) ")"
(individual_partition_clause | hash_partitions_by_quantity).
```

**[composite\_range\_partitions](#compositerangepartitions)::=**

```ebnf
= PARTITION BY RANGE "(" (column {"," column}) ")"
(subpartition_by_hash|subpartition_by_key).
```
**[subpartition\_by\_hash](#subpartitionbyhash)::=**

```ebnf
= SUBPARTITION BY HASH "(" (column {"," column}) ")" 
[hash_subparts_by_quantity].
```

**[hash_subparts_by_quantity](#hashsubpartsbyquantity)::=**

```ebnf
= SUBPARTITIONS integer [subpartition_storage_clause].
```

**[subpartition\_storage\_clause](#partitionstorageclause)::=**

组合分区的子分区存储属性语法与表存储属性table_options语法一致。

**[subpartition\_by\_key](#subpartitionbykey)::=**

```ebnf
= SUBPARTITION BY KEY algorithm "=" ("1"|"2") "(" (column {"," column}) ")" 
[hash_subparts_by_quantity].
```

**[composite\_list\_partitions](#compositelistpartitions)::=**

```ebnf
= PARTITION BY RANGE "(" (column {"," column}) ")"
(subpartition_by_hash|subpartition_by_key).
```

<span id="relationproperties" name="relationproperties"></span>

### relation\_properties

该语句用于指定创建的表的结构，包括列字段（Column）和约束（[Constraint](../通用SQL语法/constraint)）。

<span id="columndefinition" name="columndefinition"></span>

### column\_definition

该语句用于定义表的列字段，包括数据类型DataType、缺省值DEFAULT、行内约束[inline_constraint](../通用SQL语法/constraint)等列定义以及CHARACTER SET、COLLATE、COMMENT、AUTO_INCREMENT等列属性。



**ZEROFILL**

对于数值类型支持指定ZEROFILL关键字，当前仅完成语法兼容，无实际含义。

**字符集和字符序**

YashanDB mysql模式支持定义列的字符集，实际配置时列字段定义的字符集需要与数据库服务端字符集兼容，否则建表语句报错。

|Database CHARACTER SET | Column CHARACTER SET | COLLATE                                                      | DEFAULT COLLATION   |
|-----| ------------- | ------------------------------------------------------------ | ------------------- |
|ASCII| ASCII         | ASCII_BIN<br>ASCII_GENERAL_CI<br>ASCII_TOLOWER_CI         | ASCII_GENERAL_CI    |
|GBK| GBK           | GBK_BIN<br>GBK_CHINESE_CI<br>GBK_TOLOWER_CI               | GBK_CHINESE_CI      |
|UTF8| UTF8          | UTF8_BIN<br>UTF8_GENERAL_CI<br>UTF8_TOLOWER_CI            | UTF8_GENERAL_CI     |
|UTF8| UTF8MB4       | UTF8_BIN<br>UTF8_GENERAL_CI       | UTF8_GENERAL_CI     |
|UTF8| UTF8MB3       | UTF8_BIN<br>UTF8_GENERAL_CI         | UTF8_GENERAL_CI     |
|ISO88591| LATIN1        | ISO88591_BIN<br>ISO88591_TOLOWER_CI<br>ISO88591_GENERAL_CI     | ISO88591_GENERAL_CI |
|GB18030| GB18030       | GB18030_BIN<br>GB18030_TOLOWER_CI<br>GB18030_CHINESE_CI   | GB18030_CHINESE_CI  |

**BINARY**

BINARY关键字允许对CHAR、VARCHAR和TEXT类型的数据列配置排序规则。

- 配置`BINARY`关键字但没有配置`COLLATE collation_name`时，数据列的排序规则将配置为字符集对应的_BIN字符序；
- 同时配置`BINARY`和`COLLATE collation_name`时，数据列的排序规则将配置为collation_name对应字符集下的_BIN字符序。

BINARY关键字的语法约束如下：

- BINARY关键字不能重复；
- BINARY不能放在约束后面；
- BINARY不能放在COLLATE后面；
- BINARY不能放在DEFAULT后面；
- BINARY不能放在COMMENT后面；
- BINARY不能和AUTO_INCREMENT共存。

**自增列**

自增列可用于为新的记录生成唯一标识。一个表只能有一个自增列，自增列必须为索引列或组合列索引中的第一列。自增列的自增满足以下规则：

- 自增列必须为数值类型，支持INT、DOUBLE和FLOAT类型列配置自增列。
- 不对自增字段赋值，数据库会自动将自增值（默认从1开始）填入字段中，AUTO_INCREMENT自增。
- 对自增字段赋0或null值，数据库会自动将自增值填入字段中，AUTO_INCREMENT自增。
- 直接使用大于AUTO_INCREMENT的值X，数据库会将X填入字段并修改AUTO_INCREMENT = X + 1。
- 使用小于AUTO_INCREMENT但不冲突的值时，数据可以插入，但AUTO_INCREMENT不变。
- 自增步长固定为1。
- 暂不支持把非自增列改成自增列。
- 如果需要重新设置自增列的起始值可以通过alter table语句。
- 自增列不能配置缺省值DEFAULT。

**ON UPDATE CURRENT_TIMESTAMP**

用于定义对应列值的自动更新机制。配置了该属性后，当同一行其他列数据变更时，自动更新该列的值为系统当下时间。

系统变量`EXPLICIT_DEFAULTS_FOR_TIMESTAMP`为FALSE（默认值）时，即使表定义中不指定该属性，也会给表中的第一个TIMESTAMP列隐式增加该属性；配置变量为TRUE后，需要显式指定`ON UPDATE CURRENT_TIMESTAMP`属性。

该参数使用规则如下：

- 当列的数据类型为TIMESTAMP或DATETIME时，支持配置该属性。

- 语句中的CURRENT_TIMESTAMP可替换为同义词CURRENT_TIMESTAMP()、NOW()、LOCALTIME、LOCALTIME()、LOCALTIMESTAMP、LOCALTIMESTAMP()。


<span id="indexdefinition" name="indexdefinition"></span>

### index\_definition

该语句用于定义索引。

- 支持使用BTree索引，HASH索引完成语法兼容。
- KEY_BLOCK_SIZE，仅完成语法兼容。

<span id="tableoptions" name="tableoptions"></span>

### table\_options

该语句用于定义表的选项，表选项包括ENGINE、AUTO_INCREMENT、CHARACTER SET、COLLATE以及ROW_FORMAT等。

目前已完成TABLESPACE、CHARACTER SET、COLLATE、AUTO_INCREMENT的功能配置，其他配置项仅为语法兼容。

配置表的CHARACTER SET和COLLATE时，配置值需要与部署时数据库服务端的配置参数兼容，否则建表语句报错。

示例（单机HEAP表）

```sql
-- 选择数据库
USE sales;

-- 表选项
create table finance_info(
    id int auto_increment primary key,
    name varchar(100) not null
) engine = InnoDB character set utf8mb4 collate utf8mb4_bin row_format = DEFAULT AUTO_INCREMENT=100;

-- 列属性
create table employee_info(
    id int auto_increment primary key comment 'primary key',
    name varchar(100) not null character set utf8mb4 collate utf8mb4_bin
);

--带索引
create table branches(
    ind int,
    name varchar(10),
    key(ind)
);
```

<span id="partitionoptions" name="partitionoptions"></span>

### partition\_options

该语句用于为表创建分区（Partition）或组合分区，可通过INFORMATION_SCHEMA.PARTITIONS视图查看所有分区和子分区信息。

在mysql模式下，创建组合分区时，一级分区支持RANGE和LIST的分区，此时子分区的类型支持为HASH和KEY。

mysql模式下，如果创建一级分区为HASH分区的组合分区，则按照yashan模式进行语法解析和执行，数据库依然可以正常提供服务。

<span id="rangepartitions" name="rangepartitions"></span>

#### range\_partitions

创建范围分区，分区列为多项时以`,`分隔。

<span id="rangevaluesclause" name="rangevaluesclause"></span>

##### range\_values\_clause

定义范围分区的上限值。

<span id="partitionstorageclause" name="partitionstorageclause"></span>

##### partition\_storage\_clause

分区的存储属性。包括为分区指定一个表空间，省略则默认为表所在的表空间。

表分区、组合分区的子分区存储属性配置语法与表存储属性一致。

示例（单机HEAP表）

```sql
CREATE TABLE orders_info1 (
    id INT,
    num INT
)
PARTITION BY RANGE (id)  (
    PARTITION p0 VALUES LESS THAN (5),
    PARTITION p1 VALUES LESS THAN (MAXVALUE)
);

-- 创建分区表，为表和分区都指定storage属性
CREATE TABLE part_storage(a INT, b CHAR(255)) ENGINE=InnoDB
PARTITION BY RANGE(a, b)
(
	PARTITION p1 VALUES LESS THAN(1, 'a') STORAGE ENGINE=InnoDB,
	PARTITION p2 VALUES LESS THAN(10, 'c') STORAGE ENGINE=InnoDB,
	PARTITION p3 VALUES LESS THAN(MAXVALUE, MAXVALUE) STORAGE ENGINE=InnoDB
); 

-- 创建索引，为索引指定storage属性
CREATE INDEX idx_part_storage_2 
ON part_storage(b) 
STORAGE (MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0);
```

<span id="listpartitions" name="listpartitions"></span>

#### list\_partitions

创建列表分区，分区列为多项时以`,`分隔。

<span id="listvaluesclause" name="listvaluesclause"></span>

##### list\_values\_clause

定义列表分区的列表内容。

<span id="listvalues" name="listvalues"></span>

**list_values**

分区列为多项时，须用`,`分隔并与分区列一一对应，该列表内容须指定为字面量或NULL。

示例（单机HEAP表）

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

<span id="hashpartitions" name="hashpartitions"></span>

#### hash\_partitions

创建哈希分区，分区列为多项时以`,`分隔，哈希分区有如下两种创建方式：

- 逐个指定哈希分区。
- 使用hash分数批量指定哈希分区。

<span id="individualpartitionclause" name="individualpartitionclause"></span>

##### individual\_partition\_clause

本语句用于逐个指定哈希分区，对每个分区分别定义其名称和存储属性，分区之间用`,`分隔。

<span id="hashpartitionsbyquantity" name="hashpartitionsbyquantity"></span>

##### hash\_partitions\_by\_quantity

本语句用于按hash分数批量指定哈希分区，所有分区名称由系统生成。

**hash_partition_quantity**

表示hash分区的数量。

**STORE IN**

STORE IN子句描述哈希分区所属的表空间，表空间可指定多个，且数量不需要与分区的数量一致， 系统将按本语句中表空间指定的顺序进行循环归属。

不指定本语句时，系统默认使用表所在的表空间作为所有分区的表空间。

示例（单机HEAP表）

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

CREATE TABLE subpart_hash(c1 int, c2 int)
  PARTITION by HASH(c1)
  SUBPARTITION by HASH(c1)
  SUBPARTITIONS 2
  STORE IN (users)
  (PARTITION p1, PARTITION p2);
```

<span id="rangecolumnspartitions" name="rangecolumnspartitions"></span>

#### range\_columns\_partitions

基于一个或多个列的取值范围进行分区，分区依据是这些列的值组成的元组。

示例（单机HEAP表）

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

<span id="listcolumnspartitions" name="listcolumnspartitions"></span>

#### list\_columns\_partitions

基于一个或多个列的取值列表进行分区，分区依据是这些列的值组成的元组。

示例（单机HEAP表）

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

<span id="linearhashpartitions" name="linearhashpartitions"></span>

#### linear\_hash\_partitions

基于线性对数算法来确定分区，适用于需要快速分区管理和数据分布相对均匀的场景。

示例（单机HEAP表）

```sql
CREATE TABLE sales_info_hash (
    id INT, 
    name CHAR(5), 
    saltime DATE
)
PARTITION BY LINEAR HASH(saltime)
PARTITIONS 6;
```

<span id="linearkeypartitions" name="linearkeypartitions"></span>

#### linear\_key\_partitions

基于线性对数算法来确定分区，适用于需要快速分区管理的场景。

示例（单机HEAP表）

```sql
CREATE TABLE sales_info_hash1 (
    id INT NOT NULL,
    name CHAR(5),
    altime DATE
)
PARTITION BY LINEAR KEY (id)
PARTITIONS 3;
```

<span id="compositerangepartitions" name="compositerangepartitions"></span>

#### composite_range_partitions

定义范围分区的组合分区，每个范围分区下可以定义hash或key子分区。

<span id="subpartitionbyhash" name="subpartitionbyhash"></span>

##### subpartition_by_hash

定义哈希子分区的分区列，也可以指定子分区的数量。

<span id="hashsubpartsbyquantity" name="hashsubpartsbyquantity"></span>

##### hash_subparts_by_quantity

使用hash分数批量定义哈希子分区。

<span id="subpartitionbykey" name="subpartitionbykey"></span>

##### subpartition_by_key

定义哈希子分区的分区列，也可以指定子分区的数量。

**algorithm**

用于指定哈希算法，目前仅用于兼容，无实际含义。

示例（单机HEAP表）

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

<span id="compositelistpartitions" name="compositelistpartitions"></span>

#### composite_list_partitions

定义列表分区的组合分区，每个列表分区下可以定义hash或key子分区。
