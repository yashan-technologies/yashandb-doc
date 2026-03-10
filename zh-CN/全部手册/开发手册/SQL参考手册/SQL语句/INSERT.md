## 通用描述

INSERT用于对数据库中的表或视图的基表插入数据。

在单机部署中，支持单次对某一张表插入数据，也支持同时对多张表插入数据。

基于视图对其基表插入数据时，约束如下：

- 不适用于存算一体分布式集群部署。
- 目标视图需满足如下条件：
	- 视图的所有基表均为HEAP表，且视图不包含分组、聚合、去重、ROWNUM或CONNECT BY操作。
	- 视图中来自同一个基表的数据ROWID唯一。
	- 多基表视图中，要求某个基表存在列与其他所有基表的主键约束或唯一约束列都具备等值连接条件，才能基于视图INSERT该表数据。
	- 视图的基表不可以是基于dblink的远端表。
- INSERT的列必须对应基表的列（不能是其他表达式类型）。
- 同时INSERT多个列时，这些列必须来自同一个基表。
- INSERT ALL语句无法基于视图对其基表进行操作，只能直接对表操作。
- 包含ON DUPLICATE KEY UPDATE的INSERT语句无法基于视图对其基表进行操作，只能直接对表操作。

默认情况下，在INSERT的事务（Transaction）被提交（Commit）前，其他会话无法查询到被插入的新数据，可以按需开启自动提交（SET AUTOCOMMIT ON）使其他会话也能及时查询到新数据。

## 语句定义

**insert::=**

```ebnf+diagram
syntax::= INSERT [hint] (single_table_insert|multi_table_insert)
```

**[single\_table\_insert](#singletableinsert)::=**

```ebnf+diagram
syntax::= single_insert_into_clause (insert_values_clause|subquery) [on_duplicate_clause|returning_clause]
```

**[single\_insert\_into\_clause](#singleinsertintoclause)::=**

```ebnf+diagram
syntax::= INTO table_reference [t_alias] [("(" column_name ")") {"," ("(" column_name ")")}]
```

**[table\_reference](#tablereference)::=**

```ebnf+diagram
syntax::= [schema "." ] (table_name [dblink] [partition_extension_clause] | view_name)
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**[insert\_values\_clause](#insertvaluesclause)::=**

```ebnf+diagram
syntax::= VALUES ("(" (expr|DEFAULT|udt_expr) {","(expr|DEFAULT|udt_expr)} ")")
{"," ("(" (expr|DEFAULT|udt_expr) {","(expr|DEFAULT|udt_expr)} ")")}
```

**[on\_duplicate\_clause](#onduplicateclause)::=**

```ebnf+diagram
syntax::= ON DUPLICATE KEY UPDATE (set_clause) {"," (set_clause)}
```

**[set\_clause](#setclause)::=**

```ebnf+diagram
syntax::= column_name "=" (DEFAULT|expr|udt_expr|VALUES(column_name_1))
```

**[returning_clause](#returningclause)::=**

```ebnf+diagram
syntax::= (RETURN|RETURNING) (expr) {"," (expr)} INTO (variable) {"," (variable)}
```

**[multi\_table\_insert](#multitableinsert)::=**

```ebnf+diagram
syntax::= (ALL (multi_insert_into_clause [insert_values_clause])
{" " (multi_insert_into_clause [insert_values_clause])} | conditional_insert_clause) subquery
```

**[multi\_insert\_into\_clause](#multiinsertintoclause)::=**

```ebnf+diagram
syntax::= INTO table_reference ["(" (column_name) {"," (column_name)} ")"]
```

**[conditional\_insert\_clause](#conditionalinsertclause)::=**

```ebnf+diagram
syntax::= [(ALL | FIRST)] (WHEN condition THEN (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])})
{" " (WHEN condition THEN (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])})}
[ELSE (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])}]
```

### 1. hint

该语句用于提出给定的方案到优化器（Optimizer），使其按照此方案生成语句的执行计划，更多详情请查阅[hint说明](../通用SQL语法/hint)。

> **Note**: 
>
> LSC表支持bulkload hint加速大批量数据的导入，支持deduplicate hint在插入或BULKLOAD导入时进行去重。

<span id="singletableinsert" name="singletableinsert" class="yaslink"></span>

### 2. single\_table\_insert

对单张表执行INSERT操作的语句。

<span id="singleinsertintoclause" name="singleinsertintoclause" class="yaslink"></span>

#### 2.1. single\_insert\_into\_clause

该语句用于指定要插入数据的表（包括本地数据库的表或[远端表](../通用SQL语法/dblink/dblink语法说明)）及列字段，可为表定义一个别名。

##### 2.1.1. column\_name

列字段，指定多个时以`,`分隔，也可全部省略，表示按表的列字段定义顺序插入所有列项。而在指定列字段时，对于非空且没有DEFAULT值的列字段必须指定。

在列字段上定义的所有约束，对插入的数据也会起到约束作用，如违反约束则插入数据失败并提示错误。

示例

```sql
-- area表的DHQ列字段为非空项，提示错误
INSERT INTO area VALUES ('01','0000','');
YAS-04006 cannot insert NULL value to column DHQ
  
-- area表的DHQ列字段非空且定义了DEFAULT值，插入成功
INSERT INTO area (area_no) VALUES ('07');
  
-- branches表的BRANCH_NAME列字段非空且无DEFAULT值，则必须指定该列插入，否则提示错误
INSERT INTO branches (branch_no) VALUES ('0202');
YAS-04006 cannot insert NULL value to column BRANCH_NAME
```

<span id="tablereference" name="tablereference" class="yaslink"></span>

##### 2.1.2. table\_reference

该语句用于指定要插入数据的对象，可以为表名称（包括本地数据库的表或[远端表](../通用SQL语法/dblink/dblink语法说明)）、表分区名称或视图名称。

对于分区表，如未显式指定分区对象，由系统根据分区项字段的值判断要插入的表分区。而在显式指定时：

*   当分区表类型为RANGE时，新插入记录的分区项字段值请注意不要超出其所在分区的界值，否则会插入失败并提示错误。
*   当分区表类型为LIST时，新插入记录的分区项字段值请注意数据值在其所在分区的列表范围内，否则会插入失败并提示错误。

示例（单机/共享集群/分布式集群部署）

```sql
-- sales_info_range为一张范围分区表，已有p_sales_info_range_1（VALUES LESS THAN('2011')）、p_sales_info_range_2（VALUES LESS THAN('2021')）和p_sales_info_range_3（VALUES LESS THAN('2031')）三个分区
  
-- 未指定分区时，按分区项判断界值，插入数据成功
INSERT INTO sales_info_range VALUES ('2012','01','0103','11002',5,100,'');
  
-- 插入超过分区最大值的数据失败
INSERT INTO sales_info_range VALUES ('2040','01','0103','11002',5,100,'');
YAS-02115 partition key does not map to any partition
  
-- '2012'与p1分区的界值冲突，插入数据失败
INSERT INTO sales_info_range PARTITION (p_sales_info_range_1) VALUES ('2012','01','0103','11002',5,100,'');
YAS-02120 the partition number is invalid or out-of-range
```

示例（HEAP表）

```sql
-- 单基表视图
CREATE VIEW view_dml AS SELECT * FROM area WHERE EXISTS (SELECT 1 FROM dual);
INSERT INTO view_dml VALUES('06','SouthwestChina','Chengdu');
SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
06      SouthwestChina                                                          Chengdu


-- 多基表视图：视图中area表的area_name列与branches表的主键列branch_no进行了等值连接，保证了area表的数据在视图中最多仅出现一次，因此该视图中属于area表的列可被操作。
CREATE OR replace VIEW view_dml AS SELECT a.area_no, a.area_name, b.branch_no, b.branch_name FROM area a, branches b WHERE area_name = branch_no;

INSERT INTO view_dml(area_no, area_name) VALUES('07','NorthwestChina');
SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
07      NorthwestChina                                                          ShenZhen


-- 而branches表并未有任何列与area表的主键或唯一索引进行关联，在view_dml视图中属于branches表的列无法被操作
INSERT INTO view_dml(branch_no, branch_name) VALUES('07','NorthwestChina');
[1:13]YAS-04942 cannot modify a column which maps to a non key-preserved table
```

###### 2.1.2.1. dblink

该语句表示要插入数据的是远端表，详细说明参见[dblink](../通用SQL语法/dblink/dblink语法说明)。

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

###### 2.1.2.2. partition\_extension\_clause

该语句用于指定分区或子分区。

指定分区或子分区的方式均有如下两种：

- 根据名称
- 根据提供的键值

**PARTITION (partition)**

该语句用于根据名称直接获取分区对象。

示例（单机部署）

```sql
INSERT INTO sales_info_range PARTITION (p_sales_info_range_3) VALUES ('2029','01','0103','11002',5,100,'');
```

**PARTITION FOR (partition_key_value)**

该语句用于根据提供的键值（Key Value）获取分区对象。此时会将键值与表分区界值比较，计算得到分区对象，键值可为界值范围内的任意值。键值的个数须与分区列个数对应，多项用`,`分隔。

示例（单机部署）

```sql
INSERT INTO sales_info_range PARTITION FOR('2015') VALUES ('2012','01','0103','11002',5,100,'');
```

**SUBPARTITION (subpartition)**

该语句用于根据名称直接获取子分区对象。

存算一体分布式集群部署中，由于一级分区为hash分配，只有在用户知晓数据会落入某个分区（例如可以通过该分区中已存在的对应分区键数据进行判断）时，才可使用本方式指定分区插入数据，否则可能出现数据插入失败情况。

示例（单机/共享集群/分布式集群部署）

```sql
-- 获得sales_info表的子分区名称
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
-- 以下输出以单机为例
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

-- 选择其中一个子分区指定插入数据
INSERT INTO sales_info SUBPARTITION(P_SALES_INFO_1_SP_SALES_INFO_1)
VALUES ('2002','01','0402','11002',5,100,'');
```

**SUBPARTITION FOR (subpartition_key_value)**

该语句用于根据提供的键值（Key Value）获取子分区对象。此时会将键值与表分区界值比较，计算得到子分区对象，键值可为界值范围内的任意值。键值的个数须与分区列和子分区列个数对应，多项用`,`分隔。

示例

```sql
INSERT INTO sales_info SUBPARTITION FOR('0103','2018') 
VALUES ('2012','01','0103','11002',5,100,'');
```

#### 2.2. subquery

该语句用于指定要插入的数据从一个子查询中获得。插入对象的列项需与子查询的select_list列项按顺序一一对应。

查看[SELECT](./SELECT)文档中的subquery部分可获得子查询相关详细信息。

示例

```sql
INSERT INTO sales_info SELECT '2021','01',a.branch_no,'11002',100,1000,'' FROM branches a WHERE area_no ='02';
```

<span id="insertvaluesclause" name="insertvaluesclause" class="yaslink"></span>

#### 2.3. insert\_values\_clause

该语句用于按[single_insert_into_clause](#singleinsertintoclause)中定义的列字段一一指定对应的数据值。

YashanDB支持按如下语句对列字段赋值：

*   [expr](../通用SQL语法/expr)表达式
	
	- 当表达式的结果与列字段定义的数据类型不一致时，系统会先进行数据类型转换，转换失败时则返回错误。

	- 当表达式为函数时，函数的入参不能为子查询。

*   DEFAULT

	- 如对应的列上已定义了DEFAULT值，则插入的数据为该DEFAULT值，否则为NULL，基于这个规则，如对应的列存在非空约束，则插入数据失败。

	- 对包含了虚拟列的表插入数据时，如果INSERT语句不指定列名，VALUES必须是全量的列，虚拟列对应的值必须指定为DEFAULT；如果指定列名，要么不指定虚拟列，如果指定虚拟列则对应的值必须是DEFAULT。

*   对于UDT列字段，通过对象初始化方法赋值，详见[用户自定义类型](../数据类型/用户自定义类型)中描述。

VALUES关键字后可以跟多组值，代表一条SQL语句可以插入多组值，这组值的上限为32768个，超过时将返回YAS-04816错误。

示例

```sql
-area表上的DHQ字段已定义了DEFAULT值'ShenZhen',则按此值插入数据成功
INSERT INTO area VALUES ('08',33005,DEFAULT);
  
-- branches表上的BRANCH_NAME字段非空且无DEFAULT值，则按NULL值插入数据失败
INSERT INTO BRANCHES VALUES ('0202',DEFAULT,'','02');
YAS-04006 cannot insert NULL value to column BRANCH_NAME

-- area表的area_no字段插入两组值，插入成功
INSERT INTO area (area_no) VALUES ('20'),('21');

ROLLBACK;
```

<span id="onduplicateclause" name="onduplicateclause" class="yaslink"></span>

#### 2.4. on\_duplicate\_clause

该语句用于指定当插入的数据导致表的唯一约束（包括唯一索引）冲突时，不报错，而是对冲突行使用set_clause执行更新。

当表中存在多个唯一约束时，系统将只针对第一个约束项（按约束项的声明顺序确定）所产生的冲突行进行本操作处理。

需注意的是，在存算一体分布式集群部署中不能对分布表一级分区键执行本语句操作，否则返回失败。

<span id="setclause" name="setclause" class="yaslink"></span>

##### 2.4.1. set\_clause

指定更新的字段和值，指定值的规则和要求与[insert_values_clause](#insertvaluesclause)一致，不满足要求时更新失败，系统提示错误。

**column_name_1**

在单机HEAP表中，可以使用column_name = VALUES (column_name_1)定义冲突后更新的列值映射，使用约束如下：

- column_name与column_name_1属于同一个HEAP表的不同列。
- column_name_1列的数据类型需支持[隐式转换](../数据类型/数据类型转换)为column_name列的数据类型。

示例1（单机/共享集群/分布式集群部署）

```sql
-- 为area表的DHQ字段增加唯一约束，且在area_no上已存在主键约束
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           Shanghai       
02      WestChina           Chengdu        
03      SouthChina           Guangzhou      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  

ALTER TABLE area ADD CONSTRAINT c_area_unique UNIQUE (DHQ);

-- 插入的数据导致DHQ唯一约束冲突时，通过set_clause将冲突值修改，更新冲突行
INSERT INTO area VALUES('08',33005,'Shanghai') ON DUPLICATE KEY UPDATE DHQ = 'XiAn';
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           XiAn       
02      WestChina           Chengdu        
03      SouthChina           Guangzhou      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  

-- area表的area_no和DHQ列均存在唯一约束，同时冲突时set_clause只作用于第一项约束的记录
INSERT INTO area VALUES('03',33006,'Chengdu') ON DUPLICATE KEY UPDATE DHQ = 'Shenzhen';
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           XiAn       
02      WestChina           Chengdu        
03      SouthChina           Shenzhen      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  
```

示例2（存算一体分布式集群部署）

```sql
-- 创建area0表，并为area表的DHQ字段增加唯一约束
CREATE DUPLICATED TABLE area0
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL UNIQUE);
INSERT INTO area0 VALUES ('01','EastChina','Shanghai');
INSERT INTO area0 VALUES ('02','WestChina','Chengdu');
INSERT INTO area0 VALUES ('03','SouthChina','Guangzhou');
INSERT INTO area0 VALUES ('04','NorthChina','Beijing');
INSERT INTO area0 VALUES ('05','CentralChina','Wuhan');

-- 插入的数据导致DHQ唯一约束冲突时，通过set_clause将冲突值修改，更新冲突行
INSERT INTO area0 VALUES('08',33005,'Shanghai') ON DUPLICATE KEY UPDATE DHQ = 'XiAn';

SELECT area_no,area_name,DHQ FROM area0;
AREA_NO AREA_NAME                     DHQ            
------- ----------------------------- ---------------
01      EastChina                        XiAn  
02      WestChina                        Chengdu          
03      SouthChina                        Guangzhou        
04      NorthChina                        Beijing                     
05      CentralChina                        Wuhan           
```

示例3（单机HEAP表）

```sql
-- 创建area0表，并为area表的area_no字段增加唯一约束
CREATE TABLE area0
(area_no CHAR(2) NOT NULL UNIQUE,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen');
INSERT INTO area0 VALUES ('01','EastChina','Shanghai');
INSERT INTO area0 VALUES ('02','WestChina','Chengdu');
INSERT INTO area0 VALUES ('03','SouthChina','Guangzhou');
INSERT INTO area0 VALUES ('04','NorthChina','Beijing');
INSERT INTO area0 VALUES ('05','CentralChina','Wuhan');

-- 插入的数据导致DHQ唯一约束冲突时，通过set_clause将冲突值修改，更新冲突行
INSERT INTO area0 VALUES('01','EastChina','XiAn') ON DUPLICATE KEY UPDATE area_no = VALUES(area_no), area_name = VALUES(area_name), DHQ = VALUES(DHQ);

SELECT area_no,area_name,DHQ FROM area0;
AREA_NO AREA_NAME                     DHQ            
------- ----------------------------- ---------------
01      EastChina                        XiAn  
02      WestChina                        Chengdu          
03      SouthChina                        Guangzhou        
04      NorthChina                        Beijing                     
05      CentralChina                        Wuhan           
```

<span id="returningclause" name="returningclause" class="yaslink"></span>

#### 2.5. returning\_clause

本语句表示在插入数据后返回一个结果集，并将结果集赋值给指定的变量。本语句只可与[insert_values_clause](#insertvaluesclause)结合使用。

**expr**

指定结果列，多列以`,`分隔，每一列为一个[通用表达式](../通用SQL语法/expr)，但不能为序列、伪列、聚集函数、窗口函数、子语句，且表达式中不允许存在绑定参数。

**variable**

与expr一一对应的赋值变量，根据运用的语句不同，variable可以为一个已声明的变量，或者为一个绑定参数。

本语句可以在过程体或者驱动客户端程序中使用，PL手册的[DML Statement](../../PL参考手册/PL语句/DML Statement)和[EXECUTE Statement](../../PL参考手册/PL语句/EXECUTE Statement)中分别提供了该语句作为静态SQL和动态SQL在过程体中的使用示例。

<span id="multitableinsert" name="multitableinsert" class="yaslink"></span>

### 3. multi\_table\_insert

本语句适用于单机部署中的HEAP表，对一张或多张表同时进行INSERT操作，多张表的操作以空格分隔。

可以指定按值插入（insert_values_clause）或按子查询插入（subquery）。

该语句要求整个语句中有且仅有一个子查询，并且必须位于末尾。

<span id="multiinsertintoclause" name="multiinsertintoclause" class="yaslink"></span>

#### 3.1. multi\_insert\_into\_clause

除不可以为待插入的表指定别名外，该语句与[single_insert_into_clause](#singleinsertintoclause)保持一致。

多表插入时，会按照表的出现次数申请对应数量的cursor，可申请的最多cursor个数为CURSOR_POOL_SIZE / ((2 * (DB_BLOCK_SIZE + 6000)) + 2920)。

#### 3.2. insert\_values\_clause

该语句用于按[multi_insert_into_clause](#multiinsertintoclause)中定义的列字段一一指定对应的数据值。

YashanDB支持按如下语句对列字段赋值：

- [expr](../通用SQL语法/expr)表达式
	
	- 当表达式的结果与列字段定义的数据类型不一致时，系统会先进行数据类型转换，转换失败时则返回错误。

	- 表达式不能为UDT构造函数、目标类型为UDT的cast函数和treat函数。

	- 当表达式为函数时，函数的入参不能为子查询。

- DEFAULT

	如对应的列上已定义了DEFAULT值，则插入的数据为该DEFAULT值，否则为NULL，基于这个规则，如对应的列存在非空约束，则插入数据失败。

- 对于UDT列字段，通过对象初始化方法赋值，详见[用户自定义类型](../数据类型/用户自定义类型)中描述。

VALUES关键字后可以跟多组值，代表一条SQL语句可以插入多组值，这组值的上限为32768个，超过时将返回YAS-04816错误。

<span id="conditionalinsertclause" name="conditionalinsertclause" class="yaslink"></span>

#### 3.3. conditional\_insert\_clause

执行多表条件插入时，数据库会过滤每个WHEN子句后的条件，并且仅执行条件为真的插入语句，跳过条件为假的插入语句。

使用多表条件插入的约束如下：

- 一条INSERT语句中最多含有127个WHEN子句。

- WHEN条件子句、insert_values_clause以及子查询中不能使用ROWID、ROWSCN。

##### 3.3.1. ALL

条件插入中默认关键字为ALL，数据库会按顺序执行多表插入。

##### 3.3.2. FIRST

若指定FIRST关键字，数据库按顺序过滤每个WHEN子句的条件，仅执行第一个WHEN子句条件为真的插入语句，跳过其它WHEN子句的插入语句。

##### 3.3.3. ELSE

若存在ELSE子句，仅当所有WHEN子句条件为假时，才会执行ELSE子句后的插入语句。

#### 3.4. subquery

可以基于subquery为[multi_insert_into_clause](#multiinsertintoclause)中定义的列字段一一指定对应的数据值。

- 当所有表都使用insert_values_clause时，可以使用SELECT * FROM DUAL语句作为子查询。
- 子查询不能包括UDT构造函数、目标类型为UDT的cast函数和treat函数。
- 若子查询返回结果集为空，则所有表都不执行插入。

示例（单机HEAP表）

```sql
-- 语句后面必须有subquery           
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches VALUES('0002','Nanshan','00','----')
           INTO branches VALUES('0003','Futian','00','----')
		   SELECT * FROM DUAL;
		   
-- subquery必须放在语句最后面
ROLLBACK;
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches 
		   INTO branches VALUES('0003','Futian','00','----')
		   SELECT '0002','Nanshan','00',b.address FROM branches b WHERE b.branch_no='0101';	
           
-- 当subquery查询结果为空时，所有数据均不会插入
ROLLBACK;
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches 
		   INTO branches VALUES('0003','Futian','00','----')
		   SELECT '0002','Nanshan','00',b.address FROM branches b WHERE b.branch_no='0109';
```
