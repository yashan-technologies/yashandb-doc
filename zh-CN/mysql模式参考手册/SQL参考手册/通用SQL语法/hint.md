
**hint::=**

```ebnf
= "/*+" hint_name [hint_intersperse] {hint_name [hint_intersperse]} "*/".
```

SQL在查询优化阶段，优化器根据内定规则确定执行计划；作为辅助方法，开发者可以通过在语句中书写hint，指示优化器改变执行路径，以便快速的查询数据。 



优化器可以根据系统统计信息动态调整执行计划，使用hint则对执行计划进行了唯一指定，所以应该被谨慎使用，只有在已获取到足够的统计信息，确认改变执行路径是对性能更优的方式，才建议给语句增加hint。当语句涉及的表结构、业务场景或数据量等信息发生变化时，应通过[查看执行计划](../../../开发手册/SQL参考手册/SQL语句/EXPLAIN)等方式，重新审视该hint是否合适。



hint有时候会失效，如在hint中指定了非法的顺序（OUTER JOIN则不允许指定顺序），或者指定了非法的JOIN类型等。正常情况下，优化器会优先选择有效的hint，即使该hint的代价更高，但是对于前述非法的hint，优化器会不选择该hint，并在执行计划中输出hint unused。

**hint_name**

hint提示项的名称，可以同时指定多项hint_name作为组合提示。

如下标识符将被作为YashanDB认可的hint提示项名称：

[FULL](#fullhint)    [INDEX](#indexhint)    [NO_INDEX](#noindexhint)    [INDEX_FFS](#indexffshint) [NO_INDEX_FFS](#noindexffshint)

[PARALLEL](#parallelhint)

[DP_MAX_JOIN_TABLES](#dpmaxjointables)

[LEADING](#leadinghint)

[NO_USE_HASH](#nousehashhint)    [NO_USE_MERGE](#nousemergehint)    [NO_USE_NL](#nousenlhint)     [USE_HASH](#usehashhint)     [USE_MERGE](#usemergehint)     [USE_NL](#usenlhint)

[SELECTIVITY](#selectivityhint)



[BATCH_MODE](#batchmode)

**hint_intersperse**

对于某些提示，需要提供更多的信息来结合指定，例如指定使用索引提示时的索引名称。

YashanDB中使用hint的规则如下：

-  只有跟在SELECT、UPDATE、INSERT、MERGE或DELETE等关键字后面，且以”/\*+ ” 作为开始（ “+“ 号后面需有空格 ），“\*/” 作为结束的语句块 ，才会被解析成hint。
- 一个关键字后跟多组”/\*+ */”时，只有第一组被解析成hint，其他忽略。
- 一个hint里某项提示出现如下情况时，优化器忽略该项提示：

  - 拼写错误或语法错误
  - 不能识别的提示项名称
  - 与其他提示项相互冲突
  - hint_intersperse里包含了查询块
  - 在SQL语句中为表对象定义了别名，而提示中未使用别名



示例



```sql
-- 创建视图
CREATE OR REPLACE VIEW b AS 
SELECT * FROM branches WHERE branch_no = '0101'; 

-- hint_intersperse里包含了查询块,该项提示被忽略
EXPLAIN SELECT /*+ FULL(b) */ *
FROM area, b
WHERE area.area_no = '04' 
AND area.area_no = b.area_no;

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3312245078                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  MERGE JOIN                    |                      |            |       102|      296( 0)|                                |
|  2 |   MERGE SORT                   |                      |            |          |             |                                |
|  3 |    TABLE ACCESS BY INDEX ROWID | AREA                 | SALES      |          |             |                                |
|* 4 |     INDEX UNIQUE SCAN          | SYS_C_18             | SALES      |         1|      148( 0)|                                |
|  5 |   MERGE SORT                   |                      |            |          |             |                                |
|* 6 |    TABLE ACCESS BY INDEX ROWID | BRANCHES             | SALES      |          |             |                                |
|* 7 |     INDEX UNIQUE SCAN          | SYS_C_20             | SALES      |         1|      148( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Predicate : access( "AREA"."AREA_NO" = "BRANCHES"."AREA_NO" )
                   filter("AREA"."AREA_NO" = "BRANCHES"."AREA_NO")
   4 - Predicate : access("AREA"."AREA_NO" = '04')              
   6 - Predicate : filter('04' = "BRANCHES"."AREA_NO")          
   7 - Predicate : access("BRANCHES"."BRANCH_NO" = '0101')      
                                                                
Hint Information :                                              
---------------------------------------------------             
                                                                
FULL(b) / unresolved

-- 在branches表上新增一个索引
CREATE INDEX idx_branches_1 ON branches(area_no);

-- 提示项冲突
EXPLAIN SELECT /*+ FULL(b) INDEX(b IDX_BRANCHES_1) */ *
FROM branches b
WHERE b.branch_no='0401' and b.area_no='04';

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3081809351                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  TABLE ACCESS BY INDEX ROWID   | BRANCHES             | SALES      |          |             |                                |
|* 2 |   INDEX UNIQUE SCAN            | SYS_C_20             | SALES      |         1|      148( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Predicate : filter("B"."AREA_NO" = '04')                 
   2 - Predicate : access("B"."BRANCH_NO" = '0401')             
                                                                
Hint Information :                                              
---------------------------------------------------             
                                                                
FULL(b) / conflict with another                                 
INDEX(b idx_branches_1) / conflict with another        
```

<span id="batchmode" name="batchmode"></span>

### BATCH_MODE提示项


使用BATCH_MODE提示项时，无需指定表名，该提示项仅适用于单机部署中HEAP表的查询与插入语句，用于指定当前语句的批量执行模式。

示例（单机HEAP表）

```sql
SELECT /*+ BATCH_MODE(2) */*
FROM orders_info o, sales_info s,finance_info f
WHERE o.salesperson = s.salsperson
AND s.branch = f.branch;
```


<span id="fullhint" name="fullhint"></span>

### FULL提示项

使用FULL提示项时，需同时使用hint_intersperse指定表名，表示对该表执行全表扫描。

示例

```sql
SELECT /*+ FULL(a) */ * FROM area a WHERE a.area_no='02';
```

<span id="indexhint" name="indexhint"></span>

### INDEX提示项

使用INDEX提示项时，需同时使用hint_intersperse指定表名 [索引名]，表示对该表执行指定的索引扫描。

当hint_intersperse中包含多个索引时，优化器从中选择代价最低的索引；当hint_intersperse中未指定任何索引时，优化器从该表上所有的索引中选择代价最低的索引。

示例（HEAP表、TAC表）

```sql
-- 在branches表上新增一个索引
CREATE INDEX idx_branches_2 ON branches(area_no,branch_no);

-- 提示多个索引，或者不指定索引，优化器将选择代价最低的索引执行扫描
SELECT /*+ INDEX(b) */ * 
FROM branches b
WHERE b.branch_no like '0%';
```

<span id="indexffshint" name="indexffshint"></span>

### INDEX_FFS提示项

使用INDEX_FFS提示项时，需同时使用hint_intersperse指定表名 [索引名]，表示对该表执行指定的索引快速全表扫描。

当hint_intersperse中包含多个索引时，优化器从中选择代价最低的索引；当hint_intersperse中未指定任何索引时，优化器从该表上所有的索引中选择代价最低的索引。

示例

```sql
-- 按上面示例，branches上已存在idx_branches_2索引
SELECT /*+ INDEX_FFS(b idx_branches_2) */ b.area_no,b.branch_no 
FROM branches b
WHERE b.area_no='04' and b.branch_no='0401';
```

<span id="noindexffshint" name="noindexffshint"></span>

### NO_INDEX_FFS提示项

使用NO_INDEX_FFS提示项时，需同时使用hint_intersperse指定表名 [索引名]，表示不使用指定的索引对该表执行快速全表扫描。

当hint_intersperse中包含多个索引时，这些索引都将不会被优化器用来做快速全表扫描，但是这些索引可以用作其它类型的索引扫描，如果该表上存在其他索引，则优化器可能会选择那些索引执行快速全表扫描（由优化器规则决定）；当hint_intersperse中未指定任何索引时，则优化器将不会使用该表上的任何索引执行快速全表扫描。

示例

```sql
-- 按上面示例，branches上已存在idx_branches_2索引
SELECT /*+ NO_INDEX_FFS(b idx_branches_2) */ b.area_no,b.branch_no
FROM branches b
WHERE b.area_no='04' and b.branch_no='0401';
```

<span id="noindexhint" name="noindexhint"></span>

### NO_INDEX提示项

使用NO_INDEX提示项时，需同时使用hint_intersperse指定表名 [索引名]，表示不使用指定的索引对该表执行索引扫描。

当hint_intersperse中包含多个索引时，这些索引都将不会被优化器使用，但如果该表上存在其他索引，则优化器可能会选择那些索引（由优化器规则决定）；当hint_intersperse中未指定任何索引时，则优化器将不会选择该表上的任何索引。

示例

```sql
SELECT /*+ NO_INDEX(b) */ * 
FROM branches b
WHERE b.branch_no like '0%';
```

<span id="parallelhint" name="parallelhint"></span>

### PARALLEL提示项


使用PARALLEL提示项，可以实现并行查询。

PARALLEL提示项支持如下语法格式：

- `/*+ PARALLEL(int)*/`：未指定对象配置并行度时，配置值对SQL语句中的所有对象生效；
- `/*+ PARALLEL(int1),  PARALLEL(int2)*/`：指定了多个并行度时，最大的并行度配置值对SQL语句中的所有对象生效；
- `/*+ PARALLEL(object1, int1),  PARALLEL(object2, int2)*/`：对不同对象指定了多个并行度时，最大的并行度配置值对SQL语句中的所有对象生效；
- `/*+ PARALLEL(object, int1),  PARALLEL(int2)*/`：int2对SQL语句中所有的对象生效，int1不生效。

通过hint所指定的并行度最大为255，超过255时会退化为255。

无法跨SELECT指定并行度，此种场景请使用数据库参数DEGREE_OF_PARALLEL来指定。

对并行查询的指定只作为一个参考项，如果优化器评估后认为并行查询不是最优计划，则不会选择生成并行计划。

示例

```sql
SELECT /*+ PARALLEL(area,4)*/ area_no FROM area;

-- 通过hint对表a和表b指定不同的并行度（3、4）时，两个表的并行度将全为4
SELECT /*+ PARALLEL(a,3) PARALLEL(b,4)*/ a.area_no,b.branch_no
FROM area a,branches b
WHERE a.area_no = b.area_no;

-- 无效，无法跨SELECT指定并行度
SELECT /*+PARALLEL(branches,4)*/ area_no FROM area
UNION
SELECT area_no FROM branches;
```


在INSERT关键字后面使用PARALLEL提示项时，表示对指定表进行并行INSERT。目前只支持INSERT INTO SELECT的INSERT并行，INSERT和SELECT可以同时指定并行，也可以只指定一个并行。

当出现下列情形之一时，INSERT并行不生效，对应语句将生成非并行执行计划。

- PARALLEL DML DISABLED，只有在会话中显式ENABLE了PARALLEL DML，INSERT语句中的PARALLEL HINT才能生效。
- INSERT MULTI VALUES、多表INSERT、INSERT ON DUPLICATE不能并行。
- 列表、临时表、DBLINK、物化视图基表不允许并行。
- 带外键约束、INSERT TRIGGER、LOB列的表不允许并行。
- XA事务不允许并行，本地已经启动并行事务后，不能再START一个XA事务，START XA事务后并行不生效。
- 可串行化事务不允许并行。

示例

```sql
DROP TABLE IF EXISTS tab_parallel_insert_src PURGE;
CREATE TABLE tab_parallel_insert_src(id int, c1 varchar(1000));

INSERT INTO tab_parallel_insert_src VALUES(0, lpad('a', 1000, 'a'));
INSERT INTO tab_parallel_insert_src VALUES(1, lpad('a', 1000, 'a'));
COMMIT;

DROP TABLE IF EXISTS tab_parallel_insert_dst purge;
CREATE TABLE tab_parallel_insert_dst(id int, c1 varchar(1000));

-- 生成非并行计划
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 2408488895                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | INSERT STATEMENT               |                      |            |          |             |                                |
|  1 |  LOAD TABLE CONVENTIONAL       | TAB_PARALLEL_INSERT_DST| REGRESS    |          |             |                                |
|  2 |   PX COORDINATOR               |                      |            |          |             |                                |
|  3 |    PX N2I LOCAL                | QUEUE_0              |            |    100000|       41( 0)|                                |
|  4 |     PX BLOCK ITERATOR RANDOM   | DEGREE_4             |            |    100000|       30( 0)|                                |
|  5 |      TABLE ACCESS FULL         | TAB_PARALLEL_INSERT_SRC| REGRESS    |    100000|       30( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   3 - PX LocalInfo: (RANDOM SENDER -> RANDOM RECEIVER : 4->1 DEGREE_4,PART_0)

 ALTER SESSION ENABLE PARALLEL DML;
 -- 生成并行INSERT执行计划
 
 PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 2408488895                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | INSERT STATEMENT               |                      |            |          |             |                                |
|  1 |  PX COORDINATOR                |                      |            |          |             |                                |
|  2 |   LOAD TABLE CONVENTIONAL      | TAB_PARALLEL_INSERT_DST| REGRESS    |          |             |                                |
|  3 |    PX BLOCK ITERATOR RANDOM    | DEGREE_4             |            |    100000|       30( 0)|                                |
|  4 |     TABLE ACCESS FULL          | TAB_PARALLEL_INSERT_SRC| REGRESS    |    100000|       30( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
```

并行DML使用限制：

- 当表在并行DML中被修改过，不能在事务内对这个表进行查询操作，包括SELECT/UPDATE/DELETE。
- 当表执行过DML，不能在同一事务中对表再进行并行DML。
- 当表执行过并行INSERT：
  - 若该表存在索引，则不能再次进行并行INSERT和非并行INSERT。
  - 若该表不存在索引，则可以多次进行并行INSERT和非并行INSERT。

并行INSERT使用建议：

- 并行INSERT适用于导入数据的场景，数据导入后立即提交，以免因为并行后的限制影响后续业务。
- 插入数据时不建议带索引，索引冲突大，对并行性能提升影响较大。
- 并行INSERT的并行数受资源限制，需合理设置并行数，执行会按照实际申请到的并行资源设置并行度。
- 并行INSERT有额外的并行资源申请分配，数据分发操作，小数据量或资源不充足的场景下不建议使用。

示例

```sql
-- INSERT指定并行
EXPLAIN INSERT /*+ parallel(tab_parallel_insert_dst,2)*/ INTO tab_parallel_insert_dst SELECT * FROM tab_parallel_insert_src;

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 1171450920                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | INSERT STATEMENT               |                      |            |          |             |                                |
|  1 |  PX COORDINATOR                |                      |            |          |             |                                |
|  2 |   LOAD TABLE CONVENTIONAL      | TAB_PARALLEL_INSERT_DST| REGRESS    |          |             |                                |
|  3 |    PX I2N LOCAL                | QUEUE_0              |            |    100000|      127( 0)|                                |
|  4 |     TABLE ACCESS FULL          | TAB_PARALLEL_INSERT_SRC| REGRESS    |    100000|      121( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   3 - PX LocalInfo: (RANDOM SENDER -> RANDOM RECEIVER : 1->2 DEGREE_1,PART_0)
```````````````

<span id="dpmaxjointables" name="dpmaxjointables"></span>

### DP_MAX_JOIN_TABLES提示项

使用DP_MAX_JOIN_TABLES提示项，可以控制DP算法支持的hash join表个数，sql语句中join的表个数超过该值，走贪心算法，可能导致计划不是最优的。小于等于该值，则走DP算法，计划为最优的，但内存开销大。配置DP_MAX_JOIN_TABLES提示项时需指定DP算法支持的最大表个数[join表数量]，对整体sql语句生效。

若sql语句中同时通过hint指定多个DP算法支持的hash join表个数时，将采用多个hint中的最大值生效。

通过hint所指定的DP算法支持的表个数最大为32，最小值为4， 超过该区间的时，会采用隐藏配置参数DP_MAX_JOIN_TABLES的值。

示例

```sql
-- 通过hint指定不同的DP_MAX_JOIN_TABLES（4，32）时，取其最大值32生效。
select /*+ DP_MAX_JOIN_TABLES(4) */ *
  from  max_dp_join_table_1 t1 
left join max_dp_join_table_t2 t2 
  on t1.a = t2.a
left join (select /*+ DP_MAX_JOIN_TABLES(32) */ t3.a, t4.b from max_dp_join_table_4 t4
                    left join max_dp_join_table_3 t3
					on t4.a = t3.a) t5 
   on t5.a = t1.a;

-- 通过hint指定的DP_MAX_JOIN_TABLES超过取值范围时，hint不生效。
select /*+ DP_MAX_JOIN_TABLES(33) */ *
from  max_dp_join_table_1 t1 left join max_dp_join_table_t2 t2 on t1.a = t2.a;
```

如果使用DP_MAX_JOIN_TABLES时，出现“no free block in sql main pool part 0” 等内存不足报错时，请将DP_MAX_JOIN_TABLES调小，或者不使用。

<span id="leadinghint" name="leadinghint"></span>

### LEADING提示项

使用LEADING提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在多表关联的查询中，提示优化器按照指定顺序访问表。

hint_intersperse中只支持以表名或别名指定顺序，大小写不敏感。

LEADING提示的表访问顺序为建议顺序，非强制，当遇到无法调整的JOIN类型（OUTER）时，则不会改变顺序。

当hint_intersperse中指定表数量超过参与JOIN的实际表数量时，优化器不接受此项提示。

当hint_intersperse中出现无效的表时，优化器不接受在该表之后指定的顺序，但仍接受该表之前所指定的顺序。

hint_intersperse中指定多张表时，表示指定的是表连接顺序的前缀，例如LEADING(b a)，((b a) c)符合指定的顺序，(c (b a))则不符合。

示例

```sql
SELECT /*+ LEADING(b e)*/ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department
AND b.branch_no = '0401'
AND d.deparment_no = '000';
```

<span id="nousehashhint" name="nousehashhint"></span>

### NO_USE_HASH提示项

使用NO_USE_HASH提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器不进行HASH JOIN 。

示例

```sql
SELECT /*+ NO_USE_HASH(d) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="nousemergehint" name="nousemergehint"></span>

### NO_USE_MERGE提示项

使用NO_USE_MERGE提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器不进行MERGE JOIN。

示例

```sql
SELECT /*+ NO_USE_MERGE(d e) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="nousenlhint" name="nousenlhint"></span>

### NO_USE_NL提示项

使用NO_USE_NL提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器不进行NEST LOOPS。

示例

```sql
SELECT /*+ NO_USE_NL(a) */ *
FROM area a, branches b
WHERE a.area_no = b.area_no
AND a.area_no='04'
AND b.branch_no='0401';
```

<span id="usehashhint" name="usehashhint"></span>

### USE_HASH提示项

使用USE_HASH提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器进行HASH JOIN 。

示例

```sql
SELECT /*+ USE_HASH(d) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="usemergehint" name="usemergehint"></span>

### USE_MERGE提示项

使用USE_MERGE提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器进行MERGE JOIN。

示例

```sql
SELECT /*+ USE_MERGE(d e) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="usenlhint" name="usenlhint"></span>

### USE_NL提示项

使用USE_NL提示项时，需同时使用hint_intersperse指定表名 [表名]，表示在指定的这些表与其他表进行关联查询时，提示优化器进行NEST LOOPS。

示例

```sql
SELECT /*+ USE_NL(a) */ *
FROM area a, branches b
WHERE a.area_no = b.area_no
AND a.area_no='04'
AND b.branch_no='0401';
```

<span id="selectivityhint" name="selectivityhint"></span>

### SELECTIVITY提示项

使用SELECTIVITY提示项时，语法与其他hint不同，SELECTIVITY提示项只可跟在filter后，表示指定这些filter的选择率，其后需跟一个0到1之间的浮点数。

AND、OR和BETWEEN的选择率无法通过hint的方式指定，只能通过计算得到。

示例

```sql
SELECT area_no
FROM area
WHERE area_no > 1 selectivity 0.8;
```

