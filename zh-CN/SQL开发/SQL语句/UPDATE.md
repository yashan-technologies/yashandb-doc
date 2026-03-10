通用描述
----

UPDATE用于更新数据库的表或视图的基表的数据。

对LSC表更新数据时，约束如下：

- 不能对LSC表执行跨分区更新。
- 不能对AC执行UPDATE操作。
- 更新LSC表冷数据的数据，需要开启ROW MOVEMENT。LSC表冷数据的更新采用乐观锁机制，事务进行期间使用缓存保留行的锁信息，事务提交时才会根据缓存信息对行加锁。并发情况下事务提交可能会加锁失败导致事务失败。
- 如需更新LSC表的主键/唯一键，需确保在同一UPDATE语句内主键/唯一键的新值仍满足唯一性。建议尽量避免使用同一语句批量更新主键/唯一键数据，以免因LSC表的数据处理机制引起不必要的唯一性校验失败相关误报。
	
	例如，如需将LSC类型的area表中主键列area_no的值全部加1，不建议直接执行`UPDATE area SET area_no = area_no + 1;`，而应从最大值开始为每个键值单独执行一条UPDATE语句加1`UPDATE area SET area_no = area_no + 1 WHERE area_no = N;`。

基于视图对其基表更新数据时，约束如下：

- 不适用于存算一体分布式集群部署。
- 目标视图需满足如下条件：
	- 视图的所有基表均为HEAP表，且视图不包含分组、聚合、去重、ROWNUM或CONNECT BY操作。
	- 视图中来自同一个基表的数据ROWID唯一。
	- 多基表视图中，要求某个基表存在列与其他所有基表的主键约束或唯一约束列都具备等值连接条件，才能基于视图UPDATE该表数据。
	- 视图的基表不可以是基于dblink的远端表。
- UPDATE的列必须对应基表的列（不能是其他表达式类型）。

默认情况下，在UPDATE的事务（Transaction）被提交（Commit）前，其他会话无法查询到被更新的数据，可以按需开启自动提交（SET AUTOCOMMIT ON）使其他会话也能及时查询到新数据。

对于被子表定义了外键约束的父表，如果要被更新的列字段为该外键项且其数据值已在子表中存在，则无法更新该父表中的此项数据。

语句定义
----

**update::=**

```ebnf+diagram
syntax::= UPDATE [hint] dml_table_expression_clause [t_alias] update_set_clause [WHERE condition] [returning_clause]
```

**[dml_table_expression_clause](#dmltableexpression)::=**

```ebnf+diagram
syntax::= [schema "."] (table_name [dblink] [partition_extension_clause] | view_name)
syntax::= ([schema "."] (table_name [dblink] [partition_extension_clause] | view_name)) | ( "(" (subquery) [subquery_restriction_clause] ")")
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**[subquery\_restriction\_clause](#subqueryrestrictionclause)::=**

```ebnf+diagram
syntax::= ((WITH) ((READ ONLY) | (CHECK OPTIION)) [CONSTRAINT constraint])
```

**[update\_set\_clause](#updatesetclause)::=**

```ebnf+diagram
syntax::= SET (column "=" (expr|DEFAULT|"(" subquery ")")|("("(column){"," (column)}")" "=""("(((expr)|DEFAULT|("(" subquery ")")){","((expr)|DEFAULT|("(" subquery ")"))}")"))){"," (column "=" (expr|DEFAULT|"(" subquery ")")|("("(column){"," (column)}")" "=""("(((expr)|DEFAULT|("(" subquery ")")){","((expr)|DEFAULT|("(" subquery ")"))}")")))}
```

**[returning_clause](#returningclause)::=**

```ebnf+diagram
syntax::= (RETURN|RETURNING) (expr) {"," (expr)} [BULK COLLECT ] INTO (variable) {"," (variable)}
```

### 1. hint

该语句用于提出给定的方案到优化器（Optimizer），使其按照此方案生成语句的执行计划，具体可查看[hint](../../全部手册/开发手册/SQL参考手册/通用SQL语法/hint)相关说明。

<span id="dmltableexpression" name="dmltableexpression" class="yaslink"></span>

### 2. dml\_table\_expression\_clause

该语句用于指定要更新数据的对象，可以为表名称（包括本地数据库的表或[远端表](../../全部手册/开发手册/SQL参考手册/通用SQL语法/dblink/dblink语法说明)）、表分区名称、视图名称或者子查询，可对其指定一个别名。

对于分区表，如未显式指定分区对象，由系统根据分区项字段的值判断要更新的表分区。在显式指定且表未被定义为ENABLE ROW MOVEMENT时：

*   当分区表类型为RANGE时，如更新该表的分区项字段，请注意不要超出其所在分区的界值，否则会更新失败并提示错误。
*   当分区表类型为LIST时，如更新该表的分区项字段，请注意数据值在其所在分区的列表项中，否则会更新失败并提示错误。

对于存算一体分布式集群部署中的分布表，不允许修改其分区键字段的值。

更新对象为子查询时，存在以下约束：

*   待更新列的基表需满足key preserved关系，即子查询的结果集能唯一对应上基表的一行。
*   待更新列对象需要出自同一张基表。
*   子查询中不能使用输出结果不唯一的算子或操作符，包括聚集算子（group by、having、grouping sets）、聚集函数、窗口函数、distinct、limit offset/fetch n rows、sample、集合操作、for update、表函数。

示例1（单机/共享集群部署）

```sql
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range PARTITION (p_sales_info_range_1);
YEAR  MONTH BRANCH PRODUCT                QUANTITY                AMOUNT SALSPERSON  
----- ----- ------ --------- --------------------- --------------------- -------------
2001  01    0201   11001                        30                   500 0201010011 
2000  12    0102   11001                        20                   300       

-- 要更新数据的此行记录位于sales_info_range 表的p_sales_info_range_1分区，其界值为'2011'，对year字段的超出界值更新将会失败
UPDATE sales_info_range PARTITION (p_sales_info_range_1) SET year='2023' WHERE year='2001';
YAS-02209 ROW MOVEMENT is not enabled

-- 未指定分区时，按分区项判断更新值不在当前分区列表中，更新数据失败
UPDATE sales_info_list SET year='2021' WHERE year='2018';
YAS-02209 ROW MOVEMENT is not enabled
  
-- 指定分区时，按分区项判断其原值和更新值均在指定分区，更新数据成功
UPDATE sales_info_list PARTITION (p_sales_info_list_1) SET year='2019' WHERE year='2018';
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_list PARTITION (p_sales_info_list_1);
YEAR  MONTH BRANCH PRODUCT                QUANTITY                AMOUNT SALSPERSON  
----- ----- ------ --------- --------------------- --------------------- -------------
2019  10    0101   11001                        20                   300
```

示例2（存算一体分布式集群部署）

```sql
--sales_info_range为一张范围分区表，该表以branch字段作为分区键,对分布表的分区键执行update将失败
UPDATE sales_info_range SET branch='0101' WHERE branch='0201';
YAS-04510 cannot update partitioning column for sharded table
```

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

#### 2.1. partition\_extension\_clause

与[INSERT](./INSERT)语句中partition_extension_clause的描述一致。

<span id="subqueryrestrictionclause" name="subqueryrestrictionclause" class="yaslink"></span>

#### 2.2. subquery\_restriction\_clause

与[CREATE VIEW](./CREATE VIEW)语句中subquery_restriction_clause的描述一致。

可缺省。

<span id="updatesetclause" name="updatesetclause" class="yaslink"></span>

### 3. update\_set\_clause

该语句用于指定要更新数据的列字段，并对其赋值。不允许对相同列进行多次更新操作。

对于UDT列字段，通过对象初始化方法赋值，详见[用户自定义类型](../../全部手册/开发手册/SQL参考手册/数据类型/用户自定义类型)中描述。 

YashanDB支持通过两种方式更新多个列字段：

方式一：`UPDATE table_name SET column = value, column = value;`

方式二：`UPDATE table_name SET (column,...,column) = (value,...value);`

YashanDB支持按如下语句对列字段赋值：

- [expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)表达式
- 子查询
- DEFAULT

当上述语句的结果与列字段定义的数据类型不一致时，系统会先进行数据类型转换，转换失败则返回错误。

使用方式二更新多个列字段有如下约束限制：

- 括号内指定的列字段需位于同一张表，否则返回错误。
- 除对所有指定列字段均赋值为DEFAULT情况外，其他情况下值的数量需与列字段数量相同。
- value中不允许同时出现子查询和表达式。
- 存算一体分布式集群部署形态LSC表模式下暂无法使用子查询对列字段赋值。

示例（HEAP表、单机TAC表）

```sql
--在area表中插入数据
INSERT INTO area VALUES ('09','Other','Shanghai1');
  
--更新多个列字段（方式一）
UPDATE area SET area_name=23*67,DHQ='Shanghai1' WHERE area_no='09';
  
--result 
SELECT area_no,area_name,DHQ FROM area WHERE area_no='09';
AREA_NO AREA_NAME      DHQ        
------- -------------- ------------
09      1541           Shanghai1

--更新多个列字段（方式二）
UPDATE area SET (area_name,DHQ) = ('WestChina','Shaanxi') WHERE area_no='09';
```

其中，当使用子查询对列字段赋值时，更新的列字段须与子查询返回的列项必须按顺序一一对应，且子查询的返回结果不能为多条。如子查询无返回结果，将以NULL值返回，此时如对应的列存在非空约束，则更新数据失败。

示例

```sql
--将上述示例插入记录的DHQ更新为'Shanghai2'
UPDATE area SET DHQ='Shanghai2' WHERE area_no='09';
  
--在area表中未检索到DHQ='Shanghai1'的记录,以NULL值对DHQ字段进行更新触发非空约束错误
UPDATE area SET DHQ = (SELECT a.DHQ FROM area a WHERE a.DHQ='Shanghai1') WHERE area_no='09';
YAS-04006 cannot insert NULL value to column DHQ
  
--子查询返回多行记录，更新提示错误
UPDATE area SET DHQ = (SELECT a.DHQ FROM area a WHERE a.DHQ LIKE 'Shanghai%') WHERE area_no='09';
[1:24]YAS-04402 query expression return multiple rows
```

当使用DEFAULT对列字段赋值时，如对应的列上已定义了DEFAULT值，则更新的数据为该DEFAULT值，否则为NULL，基于这个规则，如对应的列存在非空约束，则更新数据失败。

示例

```sql
--area表上的DHQ字段已定义了DEFAULT值'ShenZhen',则按此值更新数据成功
UPDATE area SET DHQ=DEFAULT WHERE area_no='09';
  
--branches表上的BRANCH_NAME字段非空且无DEFAULT值，则按NULL值更新数据失败
UPDATE branches SET branch_name=DEFAULT WHERE branch_no='0201';
YAS-04006 cannot insert NULL value to column BRANCH_NAME
```

### 4. WHERE condition

该语句用于指定[condition](../../全部手册/开发手册/SQL参考手册/通用SQL语法/condition)，按此条件过滤出的记录行被执行更新操作。可省略，则表示更新表的所有行。

<span id="returningclause" name="returningclause" class="yaslink"></span>

### 5. returning\_clause

本语句表示在更新数据后返回一个结果集，并将结果集赋值给指定的变量。本语句只可与[update_set_clause](#updatesetclause)结合使用。

**expr**

指定结果列，多列以`,`分隔，每一列为一个[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，但不能为序列、伪列、聚集函数、窗口函数、变量、子语句，且表达式中不允许存在绑定参数。

**variable**

与expr一一对应的赋值变量，根据运用的语句不同，variable可以为一个已声明的变量，或者为一个绑定参数。若使用了BULK COLLECT，则INTO的目标对象必须是集合类型。

本语句可以在过程体或者驱动客户端程序中使用，PL手册的[DML Statement](../../全部手册/开发手册/PL参考手册/PL语句/DML Statement)和[EXECUTE Statement](../../全部手册/开发手册/PL参考手册/PL语句/EXECUTE Statement)中分别提供了该语句作为静态SQL和动态SQL在过程体中的使用示例。
