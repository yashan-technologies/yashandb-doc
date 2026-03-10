## 通用描述

ALTER TABLE语句用于修改表的的结构、定义以及相关属性。

## 语句定义

**alter table::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name 
(rename_clause|alter_column_clause|alter_table_partition|alter_index_clause|alter_constraint_clause|AUTO_INCREMENT ["="] value)
```

**[rename_clause](#rename)::=**

```ebnf+diagram
syntax::= RENAME [TO | AS] new_table_name
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
syntax::= column dataType [(CHARACTER SET charset_name | COLLATE collation_name | BINARY | COMMENT "string" | DEFAULT default_expr | ON UPDATE CURRENT_TIMESTAMP | inline_constraint | AUTO_INCREMENT) 
{" " (CHARACTER SET charset_name | COLLATE collation_name | BINARY | COMMENT "string" | DEFAULT default_expr | ON UPDATE CURRENT_TIMESTAMP | inline_constraint)}] 
```

**rename\_column\_clause::=**

```ebnf+diagram
syntax::= RENAME COLUMN old_column_name TO new_column_name
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
index_expr，index_type的含义及使用介绍同CREATE INDEX中的[index_expr](./CREATE INDEX.html#indexexpr)，[index_type](./CREATE INDEX.html#indextype)。

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

该语句用于修改表的自增值。

<span id="rename" name="rename" class="yaslink"></span>

### rename\_clause

该语句用于对表完成重命名操作。

重命名表时，操作用户需要拥有DROP、CREATE、ALTER和INSERT操作权限。

不支持同时对多张表重命名。

不支持将表重命名到其他的SCHEMA。

`ALTER TABLE RANAME old_table_name [TO | AS] new_table_name`与`RENAME TABLE old_table_name TO new_table_name`含义相同。

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

### alter\_column\_clause

该语句用于指定对表的列字段的操作。

<span id="changecolumnclause" name="changecolumnclause" class="yaslink"></span>

#### change\_column\_clause

该语句用于对列字段的数据类型、默认值、约束等属性进行修改，同时修改多个列字段时需在`()`中以`,`分隔。

#### dataType

修改列字段的数据类型为YashanDB认可的[数据类型](../数据类型/数据类型)。

修改规则如下：

- **外键约束所在列/**：不允许修改子表和父表中相应列的数据类型。
- **索引所在列**：
  
    - 若为空表（即无数据），允许将其修改为除BLOB/TEXT外的数据类型。

    - 若为非空表，则不允许修改其数据类型。

- **分区键所在列**：不允许修改其数据类型。

- **非外键/索引/分区键所在列**：

    - **原数据类型为BLOB/TEXT**：不允许修改其数据类型。
    - **原数据类型为字符型的空列**：允许将其修改为除BLOB/TEXT外的其他数据类型。
    - **原数据类型为字符型的非空列**：允许修改为不同字符型，但仍需遵顼以下规则：

        - 不允许CHAR/VARCHAR与NCHAR/NVARCHAR交叉修改。

        - 当按长度属性从大向小修改时，必须保证该列现有数据的长度均未超过目标数据类型的上限，否则无法修改成功并提示相应错误。

        - VARCHAR长度小于8003的列不允许将长度调大超过8004。

    - **原数据类型为除BLOB/TEXT/字符类型外的空列**：允许将其修改为除BLOB/TEXT外的其他数据类型。
  
    - **原数据类型为除BLOB/TEXT/字符类型外的非空列**：

        - 不允许跨大类修改数据类型，例如不允许将数值型修改为字符型。

        - 同一数据大类中，只允许按值域、精度、长度等属性从小向大修改数据类型，例如不允许将INT修改为指定了精度的NUMBER，不允许将DATE修改为TIME。

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### modify\_column\_clause

该语句用于对列字段的数据类型、默认值、约束等属性进行修改，同时修改多个列字段时需在`()`中以`,`分隔。

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### add\_column\_clause

该语句用于为表增加列字段，同时增加多项时在`()`中以`,`分隔。COLUMN关键字可省略。

<span id="conDefdesc" name="conDefdesc" class="yaslink"></span>

#### column\_definition

该语句用于定义表的列字段，包括数据类型DataType、缺省值DEFAULT、行内约束[inline_constraint](../通用SQL语法/constraint)等列定义以及CHARACTER SET、COLLATE、COMMENT、AUTO_INCREMENT等列属性。



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



<span id="referencesclause" name="referencesclause" class="yaslink"></span>

##### references\_clause

在定义外键约束时，REFERENCES指定了外键指向的父表，及父表中与子表外键列一一对应的列字段。若仅标识父表名而省略列名，则外键会自动引用父表的主键并且外键列与主键列需要一一对应。

<span id="addtablepartition" name="addtablepartition" class="yaslink"></span>

### alter\_table\_partition

#### add\_table\_partition

该语句用于对表增加一个新的分区。

mysql模式下支持范围（Range）、列表（List）和哈希（Hash）类型的分区。

创建组合分区时，一级分区只支持RANGE和LIST的分区，且子分区的类型只能为HASH和KEY。

<span id="addrangepartitionclause" name="addrangepartitionclause" class="yaslink"></span>

##### add\_range\_partition\_clause

增加范围类型的分区，只能在当前最大分区界值之上建立分区，如表的最大分区界值被设为MAXVALUE，则不允许增加分区。

可以为新增的分区指定hash子分区，子分区的类型需要和表的定义一致。

如建表时未指定该表为组合分区表，不允许通过本语句为新增分区指定子分区。

<span id="addlistpartitionclause" name="addlistpartitionclause" class="yaslink"></span>

##### add\_list\_partition\_clause

增加列表类型的分区，如表已在列表值里定义了DEFAULT，则不允许增加分区。

分区列表值可以指定为通用表达式（[expr](../通用SQL语法/expr)）。

可以为新增的分区指定hash子分区，子分区的类型需要和表的定义一致。

如建表时未指定该表为组合分区表，不允许通过本语句为新增分区指定子分区。

当分区列数量为一并且新增分区包含多个值时，每个值需要用括号包起来，否则报错。

<span id="addhashpartitionclause" name="addhashpartitionclause" class="yaslink"></span>

##### add\_hash\_partition\_clause

增加哈希类型的分区。

<span id="partitionstorageclause" name="partitionstorageclause" class="yaslink"></span>

##### partition\_storage\_clause

可以为新增的分区指定所属表空间（缺省为表所属表空间）。

以下存储属性仅为语法兼容，如实际含义。

- STORAGE ENGINE
- COMMENT
- DATA DIRECTORY
- INDEX DIRECTORY
- MAX_ROWS
- MIN_ROWS

示例（HEAP表）

```sql
-- 为范围分区表添加分区
ALTER TABLE sales_info_range ADD PARTITION (PARTITION p_sales_info_range_4 VALUES LESS THAN('2038'));

--创建一张range-key组合分区表range_key_table
CREATE TABLE range_key_table(a INT, b INT)
PARTITION BY RANGE(a)
SUBPARTITION BY key(b)
SUBPARTITIONS 2
(PARTITION p1 VALUES LESS than(1),
PARTITION p2 VALUES LESS than(2));

--为其添加一个range分区
ALTER TABLE range_key_table ADD PARTITION (PARTITION p3 VALUES LESS THAN(3));

-- 为列表分区表添加列表分区
ALTER TABLE sales_info_list ADD PARTITION (PARTITION p_sales_info_list_3 VALUES IN ('2022'));
ALTER TABLE sales_info_list ADD PARTITION (PARTITION p_sales_info_list_4 VALUES IN (TO_CHAR(2023)));

--创建一张list-hash组合表list_hash_table
CREATE TABLE list_hash_table(a INT, b INT)
PARTITION BY LIST(a)
SUBPARTITION BY HASH(b)
SUBPARTITIONS 2
(PARTITION p1 VALUES IN (1),
PARTITION p2 VALUES IN (2));

--为其添加一个list分区并指定其list子分区
ALTER TABLE list_hash_table ADD PARTITION (PARTITION p3 VALUES IN (3));

-- 为哈希分区表添加列表分区
ALTER TABLE sales_info_hash ADD PARTITION (PARTITION p_sales_info_hash_3);
```

<span id="droptablepartition" name="droptablepartition" class="yaslink"></span>

#### drop\_table\_partition

该语句用于删除表的分区，同时删除分区里的数据。指定多个分区用`,`分隔。

该语句指定的分区中存有数据时，会同步失效表上的全局索引。存算一体分布式集群部署模式下，不管指定的分区是否存有数据，都会失效表上的全局索引。

该语句存在如下约束限制：

- 删除分区时，不允许将表上的所有分区都删除。
- 不允许删除哈希类型分区。
- 在表的分区被删除时，对应的索引分区也会被删除。
- 对于范围类型分区，删除分区后其分区界值将会向相邻大一级分区合并，这样符合此被删除分区界值范围的新增表数据将进入相邻分区中，如不存在相邻大一级分区则数据无法再插入成功。

<span id="updateindexclause" name="updateindexclause" class="yaslink"></span>

##### update\_index\_clause

指定分区被删除后，对表上的全局索引（Global Index）的处理，默认为INVALIDATE。

*   INVALIDATE GLOBAL INDEXES：将全局索引全部失效，不可用。
*   UPDATE GLOBAL INDEXES：全局索引不失效仍然可用。

使用`update_index_clause`语法时，将按照yashan模式进行解析执行，数据库正常提供服务。

示例（HEAP表）

```sql
--sales_info_range为一张范围分区表
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
year  month branch product      quantity      amount salsperson  
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

该语句用于为表增加或删除索引。

<span id="addindexclause" name="addindexclause" class="yaslink"></span>

#### add\_index\_clause

该语句用于为表增加索引。多个索引列在`()`中以`,`分隔。可以指定索引选项USING BTREE。

<span id="dropindexclause" name="dropindexclause" class="yaslink"></span>

#### drop\_index\_clause

该语句用于为表删除索引。

<span id="alterconstraintclause" name="alterconstraintclause" class="yaslink"></span>

### alter\_constraint\_clause

该语句用于为表增加或删除约束。

<span id="addconstraintclause" name="addconstraintclause" class="yaslink"></span>

#### add\_constraint\_clause

该语句用于为表增加约束。

<span id="dropconstraintclause" name="dropconstraintclause" class="yaslink"></span>

#### drop\_constraint\_clause

该语句用于为表删除约束。

示例（单机HEAP表）

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
