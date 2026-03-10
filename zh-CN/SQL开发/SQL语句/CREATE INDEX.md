## 通用描述

CREATE INDEX用于在指定的表上创建一个索引对象。在YashanDB中可创建多种类型的索引，例如：

- [唯一索引](#UNIQUE)
- [列式索引](#COLUMNAR)
- [函数索引](#columnexpression)
- [反向索引](#REVERSE)
- [分区索引](#localindexclause)
- [RTree索引](#RTREE)：仅用于ST_Geometry类型的列

## 语句定义

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

**[storage_clause定义](../../全部手册/开发手册/SQL参考手册/通用SQL语法/storage_clause)**

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

该语句用于指定创建的索引为唯一索引，即索引列字段上的数值唯一。如表中该列字段上的值存在重复，则创建唯一索引失败。

LSC表必须指定该关键字。

存算一体分布式集群部署中，分区列必须是唯一索引关键列的子集，否则报错。

示例

```sql
--在表orders_info中的id,area列创建唯一索引idx_orders_info_1
CREATE UNIQUE INDEX idx_orders_info_1 ON orders_info (id, area);
```

<span id="COLUMNAR" name="COLUMNAR" class="yaslink"></span>

### 2. COLUMNAR

该语句用于指定创建的索引为列式索引，即索引的数据内容采用列存方式存储，索引本身无序。

列式索引存在如下限制：

- 当指定为列式索引时，不能同时指定为[函数索引](#columnexpression)或[反向索引](#REVERSE)，且不能在线（ONLINE）[创建](#ONLINE)和[重建](./ALTER INDEX)（rebuild_clause）。
- 只能在单机HEAP表中创建列式索引。

示例（单机HEAP表）

```sql
--在表orders_info中的product_no列创建列式索引colidx_orders_info_1
CREATE COLUMNAR INDEX colidx_orders_info_1 ON orders_info (product_no);
```

<span id="RTREE" name="RTREE" class="yaslink"></span>

### 3. RTREE

该语句用于指定创建RTree索引。

RTree索引存在如下限制：

- RTree索引仅适用于单机部署和共享集群部署的HEAP表，且不能为临时表。

- 只能为[ST_Geometry类型](../../全部手册/开发手册/SQL参考手册/数据类型/ST_GEOMETRY)的列创建RTree索引，且每个RTree索引只能指定一个列。

- 当指定为RTree索引时，不能同时指定为[函数索引](#columnexpression)或[反向索引](#REVERSE)，且不能在线（ONLINE）[创建](#ONLINE)和[重建](./ALTER INDEX)（rebuild_clause）。

- 使用RTree索引时，不能将事务隔离级别设为[SERIALIZABLE](SET TRANSACTION)。

示例（单机HEAP表、共享集群HEAP表）

```sql
--在表geom_test中的shp列创建RTree索引idx_rtree_shp，其中shp列的数据类型为ST_Geometry
CREATE TABLE geom_test(id INT, geom_type VARCHAR(32), shp st_geometry);
CREATE RTREE INDEX idx_rtree_shp ON geom_test (shp);
```

### 4. index\_name

该语句用于指定创建的索引的名称，不可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

### 5. table\_name

该语句用于指定要创建索引的表。创建索引的操作过程中会对该表加排他锁，但在指定了ONLINE时，本操作不会阻塞对该表的并发DML事务。

<span id="indexexpr" name="indexexpr" class="yaslink"></span>

### 6. index\_expr

指定索引所基于的列或列表达式，多项组合指定时以`,`分隔。

指定规则如下：

- [LOB大对象型](../../全部手册/开发手册/SQL参考手册/数据类型/大对象型)的列不能作为索引列。

- [ST_Geometry类型](../../全部手册/开发手册/SQL参考手册/数据类型/ST_GEOMETRY)的列上只能创建RTree索引，且每个RTree索引只能指定一个列。

- 加密列上只能创建等值查询的BTREE索引。

#### 6.1. column\_name

将指定名称的列字段作为索引列。

<span id="columnexpression" name="columnexpression" class="yaslink"></span>

#### 6.2. column\_expression

将指定的表达式作为索引列，含有表达式的索引列的索引称为函数索引。

YashanDB支持以任何[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)作为索引列来建立函数索引，但存在如下约束规则：

- column_expression计算出来的结果须满足系统对索引列的所有约束限制，例如不能为LOB型，长度不能超过索引键值长度规格等。
- column_expression中包含内置函数时，该函数必须是一个可以返回确定和不变结果的非聚集函数，例如不能为SYSDATE、USERENV、SUM等函数。
- column_expression中存在自动填充行为时，不能为非固定自动填充行为的表达式，例如在日期转换时发生自动填充年月的行为，由于系统需使用当前年月这类非固定值填充，导致表达式输出的结果不能确定，所以无法建立函数索引；而对于时分秒系统固定按0填充，或者对于CHAR类型系统固定按空格补位，表达式输出结果确定，则可以建立函数索引。
- 在创建分区索引时，不能对分区键使用函数表达式。
- column_expression中不能包含用户自定义函数。
- 当指定为[RTree索引](#RTREE)时，不能同时指定为函数索引。
- 不能为LSC表创建函数索引。

示例（HEAP表、单机TAC表）

```sql
--函数索引可以为任意表达式
CREATE UNIQUE INDEX idx_orders_info_2 ON orders_info (TO_CHAR(id)||order_no);
CREATE INDEX idx_orders_info_3 ON orders_info (CAST(id AS CHAR(14)),order_no);

--字符串向DATE转换时，未指定format将导致系统获取不能保证恒定的默认格式，所以无法返回一个不变的结果
CREATE INDEX idx_orders_info_4 ON orders_info (order_date-TO_DATE('2001-01-01'));
[1:69]YAS-04399 only pure functions can be indexed
```

### 7. DESC|ASC

指定索引列的同时指定排序方式，可省略，则系统默认按升序排序。

示例（HEAP表、TAC表）

```sql
--指定索引列的升降序
CREATE INDEX idx_orders_info_4 ON orders_info (order_date DESC, TO_NUMBER(order_no) ASC);
```

<span id="indexattrclause" name="indexattrclause" class="yaslink"></span>

### 8. index\_attr\_clause

该语句用于指定索引的各项属性。

#### 8.1. TABLESPACE

该语句用于指定索引的表空间，省略则默认为当前用户所在的表空间。用户无法为建立在分布表上的索引和索引分区指定表空间，这类索引和索引分区所在的表空间将自动和表保持一致。

*   tablespace_name：指定到一个存在的表空间。
*   DEFAULT：指定到缺省表空间，即当前用户所在的表空间。

在临时表上创建的索引，其表空间只能为系统默认的temporary表空间，不可指定。

#### 8.2. INITRANS/PCTFREE/storage\_clause

该语句用于指定索引的存储属性，省略则INITRANS/PCTFREE默认为2/8，其他存储属性请参考通用SQL语法[storage](../../全部手册/开发手册/SQL参考手册/通用SQL语法/storage_clause)描述。

#### 8.3. VISIBLE|INVISIBLE

该语句用于指定创建的索引是否能被优化器（Optimizer）使用，省略则默认为VISIBLE。

#### 8.4. USABLE|UNUSABLE

该语句用于指定创建的索引是否可用，省略则默认为USABLE。

#### 8.5. GLOBAL

该语句用于显式指定创建的索引是全局索引。若不指定该关键字，YashanDB也将默认创建全局索引。分布表指定GLOBAL关键字将会报错。

<span id="ONLINE" name="ONLINE" class="yaslink"></span>

#### 8.6. ONLINE

该语句用于指定创建索引过程中是否允许并发DML操作，省略则默认不允许。

在线（Online）创建索引存在如下限制：

- 在线创建索引不适用于存算一体分布式集群部署。
- 不允许为LSC表和临时表在线创建索引。
- 不允许在线创建[列式索引](#COLUMNAR)和[RTree索引](#RTREE)。
- 在共享集群/分布式集群部署下，正在执行的create/rebuild index online在遇到实例启停时会失败。

<span id="parallel" name="parallel" class="yaslink"></span>

#### 8.7. NOPARALLEL|PARALLEL

该语句用于设置创建索引的并行度，NOPARALLEL表示不并行，不指定并行度默认按一半CPU核数的并行度并发创建索引。

LSC表不允许并行创建索引，即integer不得大于1。

**integer**

并行度值，取值范围[1,服务器CPU核数\*2]，可省略。省略时，当探测到当前表数据量为大于1G，小于当前DATA_BUFFER_SIZE参数值时，默认按一半CPU核数的并行度并发创建索引。

示例（单机HEAP表、单机TAC表）

```sql
--在线并行创建索引
DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) ONLINE PARALLEL 2;
```

#### 8.8. NOCOMPRESS|COMPRESS

该语句用于语法兼容，无实际含义。

#### 8.9. LOGGING|NOLOGGING

该语句用于指定创建索引的logging属性，可省略，省略则默认为logging。

只能在nologging表上创建nologging索引。

示例（HEAP表、TAC表）

```sql
CREATE TABLE logging_table(c1 INT);

CREATE INDEX logging_index ON logging_table(c1);

-- 只能在nologging表上创建nologging索引
CREATE INDEX nologging_index ON logging_table(c1) NOLOGGING;
YAS-02367 create nologging index is not allowed for logging table
```

<span id="REVERSE" name="REVERSE" class="yaslink"></span>

#### 8.10. NOREVERSE|REVERSE

该语句用于指定创建的索引是否为反向索引。反向索引在存入数据时将索引列字节进行反转，以离散数据，减少叶块争用。省略时默认为NOREVERSE。

不能为LSC表创建反向索引。

示例（HEAP表、TAC表）

```sql
DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) REVERSE;

DROP INDEX idx_orders_info_1;
CREATE INDEX idx_orders_info_1 ON orders_info (id) NOREVERSE;
```

<span id="readonlyclause" name="readonlyclause" class="yaslink"></span>

##### 8.10.1. readonly\_clause

语法兼容，无实际含义。

<span id="inmemoryclause" name="inmemoryclause" class="yaslink"></span>

##### 8.10.2. inmemory\_clause

语法兼容，无实际含义。

<span id="localindexclause" name="localindexclause" class="yaslink"></span>

#### 8.11. local\_index\_clause

该语句用于创建分区索引（Local Partitioned Index）。只能在分区表上建立分区索引，且索引的分区数量、分区界值必须与表的分区相同。

创建唯一索引，要保证一级分区键和二级分区键的并集需要是索引键的子集。

若LOCAL后面的语句都省略，即按如下规则创建索引分区：

*   分区数量与表分区数量一致
*   分区界值与表分区界值一致
*   表空间与在INDEX上指定的值一致
*   INITRANS/PCTFREE等存储属性与在INDEX上指定的值一致
*   USABLE/UNUSABLE开关设置为USABLE

示例（HEAP表、单机TAC表）

```sql
-- 创建本地分区索引
CREATE INDEX idx_sales_info_2 ON sales_info (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL;
```

<span id="indexhashpartitionclause" name="indexhashpartitionclause" class="yaslink"></span>

##### 8.11.1. store\_in\_partition\_clause

该语句仅被用于指定在哈希分区表上的索引分区的表空间，指定多个表分区用`,`分隔，且数量需与索引分区的数量一致。

示例（HEAP表、单机TAC表）

```sql
--指定hash分区索引的分区表空间
CREATE INDEX idx_sales_info_hash_1 ON sales_info_hash (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL STORE IN (yashan,yashan1);
```

<span id="indexpartitionclause" name="indexpartitionclause" class="yaslink"></span>

##### 8.11.2. index\_partition\_clause

指定索引分区的名称和其他属性，同时指定多个分区用`,`分隔，且数量需与索引分区的数量一致。

###### 8.11.2.1. partition\_name

指定索引分区的名称，省略则由系统生成的默认名称。

<span id="indexpartitionattrclause" name="indexpartitionattrclause" class="yaslink"></span>

###### 8.11.2.2. index\_partition\_attr\_clause

指定索引分区的如下属性：

*   表空间：省略则默认为在INDEX上指定的表空间。其中DEFAULT值等于在INDEX上指定的表空间名称。
*   INITRANS/PCTFREE：省略则默认为在INDEX上指定的存储属性值。
*   USABLE/UNUSABLE：省略则为USABLE。

示例（HEAP表、单机TAC表）

```sql
--指定索引分区属性
CREATE INDEX idx_sales_info_range_1 ON sales_info_range (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL (PARTITION p1 TABLESPACE DEFAULT INITRANS 2,PARTITION p2,PARTITION p3);
```

<span id="indexcomppartitionclause" name="indexcomppartitionclause" class="yaslink"></span>

##### 8.11.3. index\_comp\_partition\_clause

指定索引组合分区的名称和其他属性，同时指定多个分区用`,`分隔，且数量需与表分区的数量一致。

###### 8.11.3.1. store in

指定二级分区模板信息，只适用于hash分区。

<span id="indexsubpartitionclause" name="indexsubpartitionclause" class="yaslink"></span>

###### 8.11.3.2. index\_subpartition\_clause

指定索引二级分区的名称和其他属性，同时指定多个二级分区用`,`分隔，且数量需与表的对应二级分区的数量一致。

示例（存算一体分布式集群部署）

```sql
--创建默认名称的二级分区索引
CREATE UNIQUE INDEX idx_orders_info_sub ON orders_info(area, id) LOCAL;
```

示例（单机/共享集群/分布式集群部署、HEAP/TAC表）

```sql
--创建指定名称的二级分区索引
CREATE INDEX idx_sales_info_sub ON sales_info(product) LOCAL
(PARTITION ip_sales_info_1 UNUSABLE(SUBPARTITION isp_sales_info_11, SUBPARTITION isp_sales_info_12, SUBPARTITION isp_sales_info_13),
PARTITION ip_sales_info_2 USABLE (SUBPARTITION isp_sales_info_21, SUBPARTITION isp_sales_info_22, SUBPARTITION isp_sales_info_23),
PARTITION ip_sales_info_3 USABLE (SUBPARTITION isp_sales_info_31, SUBPARTITION isp_sales_info_32, SUBPARTITION isp_sales_info_33));
```

#### 8.12. COMPUTE STATISTICS

该语句用于语法兼容，无实际含义。
