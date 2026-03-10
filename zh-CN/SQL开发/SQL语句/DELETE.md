通用描述
----

DELETE用于删除数据库的表或视图的基表中的记录。

对LSC表删除数据时，约束如下：

- LSC表冷数据的删除采用乐观锁机制，事务进行期间使用缓存保留行的锁信息，事务提交时才会根据缓存信息对行加锁。并发情况下事务提交可能会加锁失败导致事务失败。

基于视图对其基表删除记录时，约束如下：

- 不适用于存算一体分布式集群部署。
- 目标视图需满足如下条件：
	- 视图的所有基表均为HEAP表，且视图不包含分组、聚合、去重、ROWNUM或CONNECT BY操作。
	- 视图中来自同一个基表的数据ROWID唯一。
	- 多基表视图中，要求某个基表存在列与其他所有基表的主键约束或唯一约束列都具备等值连接条件，才能基于视图DELETE该表数据。
	- 视图的基表不可以是基于dblink的远端表。

默认情况下，在DELETE的事务（Transaction）被提交（Commit）前，其他会话仍可以查询到被删除的数据，可以按需开启自动提交（SET AUTOCOMMIT ON）及时杜绝其他会话访问被删除的废弃数据。

对于被子表定义了外键约束的父表，如果要被删除的外键列项数据值已在子表中存在，则无法删除该父表的此行数据（除非该外键约束被定义了ON DELETE），详细描述请参考通用SQL语法[constraint](../../全部手册/开发手册/SQL参考手册/通用SQL语法/constraint（yashan模式）)的FOREIGN KEY。

语句定义
----

**delete::=**

```ebnf+diagram
syntax::= DELETE [hint] [FROM] table_reference  [WHERE condition]
```

**table\_reference::=**

```ebnf+diagram
syntax::= ([schema "." ] (table_name [dblink][partition_extension_clause] | view_name) [t_alias]) | ("(" (subquery) [subquery_restriction_clause] ")")
```

**partition\_extension\_clause::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**subquery\_restriction\_clause::=**

```ebnf+diagram
syntax::= ((WITH) ((READ ONLY) | (CHECK OPTIION)) [CONSTRAINT constraint])
```

### 1. hint

该语句用于提出给定的方案到优化器（Optimizer），使其按照此方案生成语句的执行计划。[查看hint说明](../../全部手册/开发手册/SQL参考手册/通用SQL语法/hint)。

### 2. table\_reference

该语句用于指定要删除记录的对象，可以为表名称（包括本地数据库的表或[远端表](../../全部手册/开发手册/SQL参考手册/通用SQL语法/dblink)）、表分区名称或视图名称。

删除对象为子查询时，存在以下约束：

*   待删除的基表需满足key preserved关系，即子查询的结果集能唯一对应上基表的一行。
*   若子查询中存在多张基表满足key preserved关系，将从中随机选取一张基表进行删除。
*   子查询中不能使用输出结果不唯一的算子或操作符，包括聚集算子（group by、having、grouping sets）、聚集函数、窗口函数、distinct、limit offset/fetch n rows、sample、集合操作、for update、表函数。

#### 2.1. dblink

该语句表示要删除的是远端表，详细说明参见[dblink](../../全部手册/开发手册/SQL参考手册/通用SQL语法/dblink)。

#### 2.2. partition\_extension\_clause

与[INSERT](./INSERT)语句中partition_extension_clause的描述一致。

未指定本语句时，对分区表，由系统根据分区键字段的值判断要删除记录所在的表分区。

指定本语句时，要删除的记录须存在于指定的分区中，否则无法删除成功。

#### 2.3. subquery\_restriction\_clause

与[CREATE VIEW](./CREATE VIEW)语句中subquery_restriction_clause的描述一致。

可缺省。

#### 2.4. t\_alias

定义别名。

### 3. WHERE condition

该语句用于指定[condition](../../全部手册/开发手册/SQL参考手册/通用SQL语法/condition)，按此条件过滤出的记录行被执行删除操作。可省略，则表示删除表的所有行，但该表及表上索引所占的数据空间仍然被保留。

示例1

```sql
DELETE FROM area WHERE area_no='08';
```

示例2（单机/共享集群部署）

```sql
-- orders_info表的p_orders_info_1分区中包含如下四条记录
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION (p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE                       SALESPERSON            ID 
----------------- ---------- ----- ------ -------------------------------- ------------- ----------- 
20010102020001    11001      02    0201   2022-05-01 22:55:32              0201010011            300
20210102020002    11001      02    0201   2022-05-01 22:55:32              0201008003            400
20210102020002    11002      02    0201   2022-05-01 22:55:32              0201010011            200
20210102020002    10001      02    0201   2022-05-01 22:55:32              0201008003            100
 
-- 删除指定分区的指定记录
DELETE FROM orders_info PARTITION(p_orders_info_1) WHERE ID=300;
 
-- result
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION (p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE                       SALESPERSON            ID 
----------------- ---------- ----- ------ -------------------------------- ------------- ----------- 
20210102020002    11001      02    0201   2022-05-01 22:55:32              0201008003            400
20210102020002    11002      02    0201   2022-05-01 22:55:32              0201010011            200
20210102020002    10001      02    0201   2022-05-01 22:55:32              0201008003            100
```

示例2（HEAP表）

```sql
-- branches为HEAP表，其area_no字段上存在CONSTRAINT c_branches_1 REFERENCES area(area_no) ON DELETE SET NULL外键约束定义

-- 查看area_no为'04'对应的子表记录
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='04';
BRANCH_NO BRANCH_NAME             AREA_NO ADDRESS          
--------- ----------------------- ------- -----------------
0401      Beijing                     04                         
0402      Tianjin                     04                         
0403      Dalian                     04      Dalian City             
0404      Shenyang                     04   
 
-- 对父表area执行删除 
DELETE FROM area WHERE area_no='04';
COMMIT;

-- 子表相应字段值被置为NULL
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='04';
BRANCH_NO BRANCH_NAME                                                      AREA_NO ADDRESS                                                          
--------- ---------------------------------------------------------------- ------- ---------------------------------------------------------------- 
```
