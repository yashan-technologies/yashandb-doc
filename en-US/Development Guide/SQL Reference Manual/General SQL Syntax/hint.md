
**hint::=**

```ebnf
= "/*+" hint_name [hint_intersperse] {hint_name [hint_intersperse]} "*/".
```

In the query optimization phase, the optimizer determines the execution plan based on internal rules. As an auxiliary method, developers can write hints in the statements to instruct the optimizer to change the execution path for faster data retrieval.



The optimizer can dynamically adjust the execution plan based on system statistics. Using hints uniquely specifies the execution plan, so hints should be used cautiously, only after sufficient statistics have been obtained to confirm that changing the execution path improves performance. When information about the table structure, business scenarios, or data volume changes, the appropriateness of the hint should be reassessed, for example, by reviewing the execution plan through [EXPLAIN](../SQL Statements/EXPLAIN).



Hints can become ineffective if an invalid order is specified in the hint (OUTER JOIN does not allow order specification) or an invalid JOIN type is specified. Under normal circumstances, the optimizer will prioritize valid hints, even if their cost is higher, but for the aforementioned invalid hints, the optimizer will not choose them and will output "hint unused" in the execution plan.

**hint_name**

The name of the hint item; multiple `hint_name` can be specified as a composite hint.

The following identifiers will be recognized as hint item names by YashanDB:

[FULL](#fullhint)    [INDEX](#indexhint)    [NO_INDEX](#noindexhint)    [INDEX_FFS](#indexffshint) [NO_INDEX_FFS](#noindexffshint)

[PARALLEL](#parallelhint)

[DP_MAX_JOIN_TABLES](#dpmaxjointables)

[LEADING](#leadinghint)

[NO_USE_HASH](#nousehashhint)    [NO_USE_MERGE](#nousemergehint)    [NO_USE_NL](#nousenlhint)     [USE_HASH](#usehashhint)     [USE_MERGE](#usemergehint)     [USE_NL](#usenlhint)

[SELECTIVITY](#selectivityhint)

[BULKLOAD](#bulkload)

[DEDUPLICATE](#deduplicate)

[MAX_WORKERS_PER_EXEC](#max_workers_per_exec)



[BATCH_MODE](#batchmode)

**hint_intersperse**

For certain hints, additional information needs to be provided for specification, such as the index name when specifying index hints.

The rules for using hints in YashanDB are as follows:

- Only statements that follow SELECT, UPDATE, INSERT, MERGE, or DELETE keywords, starting with "/*+ " (there must be a space after the "+") and ending with "*/", will be parsed as hints.
- When multiple sets of "/*+ */" follow a keyword, only the first set is parsed as a hint; the others are ignored.
- When a hint contains the following situations, the optimizer ignores that hint item:

  - Spelling errors or syntax errors.
  - Unrecognized hint item names.
  - Conflicts with other hint items.
  - Containing query blocks in hint_intersperse.
  - An alias is defined for a table object in the SQL statement, but the alias is not used in the hint.



***Example*** for Heap tables and TAC tables



```sql
-- Create a view
CREATE OR REPLACE VIEW b AS 
SELECT * FROM branches WHERE branch_no = '0101'; 

-- When the query block is included in hint_intersperse, the hint is ignored
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

-- Adding an index to the branches table
CREATE INDEX idx_branches_1 ON branches(area_no);

-- Hint item conflict
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

### BATCH_MODE Hint Item


When using the BATCH_MODE hint item, there is no need to specify the table name; this hint applies only to queries and insert statements of HEAP tables in Standalone Deployment and is used to specify the batch execution mode of the current statement.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT /*+ BATCH_MODE(2) */*
FROM orders_info o, sales_info s,finance_info f
WHERE o.salesperson = s.salsperson
AND s.branch = f.branch;
```


<span id="fullhint" name="fullhint"></span>

### FULL Hint Item

When using the FULL hint item, it must also use hint_intersperse to specify the table name, indicating a full table scan on that table.

***Example***

```sql
SELECT /*+ FULL(a) */ * FROM area a WHERE a.area_no='02';
```

<span id="indexhint" name="indexhint"></span>

### INDEX Hint Item

When using the INDEX hint item, it must also use hint_intersperse to specify the table name [index name], indicating the use of a specified index to scan the table.

When multiple indexes are included in hint_intersperse, the optimizer selects the index with the lowest cost; when no index is specified in hint_intersperse, the optimizer selects the index with the lowest cost from all indexes on that table.

***Example*** for Heap tables and TAC tables

```sql
-- Adding an index to the branches table
CREATE INDEX idx_branches_2 ON branches(area_no,branch_no);

-- Hinting multiple indexes or not specifying an index; the optimizer will choose the index with the lowest cost for scanning
SELECT /*+ INDEX(b) */ * 
FROM branches b
WHERE b.branch_no like '0%';
```

<span id="indexffshint" name="indexffshint"></span>

### INDEX_FFS Hint Item

When using the INDEX_FFS hint item, it must also use hint_intersperse to specify the table name [index name], indicating the execution of a rapid full table scan on the specified index for that table.

When multiple indexes are included in hint_intersperse, the optimizer selects the index with the lowest cost; when no index is specified in hint_intersperse, the optimizer chooses the index with the lowest cost from all indexes on that table.

***Example***

```sql
-- Following the above example, an index idx_branches_2 already exists on branches
SELECT /*+ INDEX_FFS(b idx_branches_2) */ b.area_no,b.branch_no 
FROM branches b
WHERE b.area_no='04' and b.branch_no='0401';
```

<span id="noindexffshint" name="noindexffshint"></span>

### NO_INDEX_FFS Hint Item

When using the NO_INDEX_FFS hint item, it must also use hint_intersperse to specify the table name [index name], indicating not to use the specified index for rapid full table scan on that table.

When multiple indexes are included in hint_intersperse, those indexes will not be used by the optimizer for rapid full table scan, but can be utilized for other types of index scans. If other indexes exist on that table, the optimizer might choose those indexes for the rapid full table scan (as determined by optimizer rules); when no index is specified in hint_intersperse, the optimizer will not use any index on that table for rapid full table scan.

***Example***

```sql
-- Following the above example, an index idx_branches_2 already exists on branches
SELECT /*+ NO_INDEX_FFS(b idx_branches_2) */ b.area_no,b.branch_no
FROM branches b
WHERE b.area_no='04' and b.branch_no='0401';
```

<span id="noindexhint" name="noindexhint"></span>

### NO_INDEX Hint Item

When using the NO_INDEX hint item, it must also use hint_intersperse to specify the table name [index name], indicating to not use the specified index for index scanning on that table.

When multiple indexes are included in hint_intersperse, those indexes will not be used by the optimizer, but if other indexes exist on that table, the optimizer may choose those indexes (as determined by optimizer rules); when no index is specified in hint_intersperse, the optimizer will not choose any index on that table.

***Example***

```sql
SELECT /*+ NO_INDEX(b) */ * 
FROM branches b
WHERE b.branch_no like '0%';
```

<span id="parallelhint" name="parallelhint"></span>

### PARALLEL Hint Item


When using the PARALLEL hint item, it allows for parallel querying.

The PARALLEL hint item supports the following syntax formats:

- `/*+ PARALLEL(int)*/`: When no specific object is specified for the degree of parallelism, the configured value applies to all objects in the SQL statement.
- `/*+ PARALLEL(int1),  PARALLEL(int2)*/`: When multiple degrees of parallelism are specified, the maximum value takes effect for all objects in the SQL statement.
- `/*+ PARALLEL(object1, int1),  PARALLEL(object2, int2)*/`: When multiple degrees of parallelism are specified to the different object in the SQL Statement, the maximum value takes effect for all objects in the SQL statement.
- `/*+ PARALLEL(object, int1),  PARALLEL(int2)*/`: The int2 setting applies to all objects in the SQL statement, whereas int1 has no effect.

The maximum degree of parallelism specified via the hint is 255; exceeding 255 will degrade to 255.

It is not possible to specify parallelism across SELECT statements; in such scenarios, use the database parameter DEGREE_OF_PARALLEL to specify.

The specification of parallel queries serves only as a reference; if the optimizer evaluates that parallel querying is not an optimal plan, it will not choose to generate a parallel plan.

***Example***

```sql
SELECT /*+ PARALLEL(area,4)*/ area_no FROM area;

-- Specifying different degrees of parallelism (3,4) for tables a and b; both tables will have a parallelism of 4
SELECT /*+ PARALLEL(a,3) PARALLEL(b,4)*/ a.area_no,b.branch_no
FROM area a,branches b
WHERE a.area_no = b.area_no;

-- Invalid; cannot specify parallelism across SELECT statements
SELECT /*+PARALLEL(branches,4)*/ area_no FROM area
UNION
SELECT area_no FROM branches;
```


When using the PARALLEL hint after the INSERT keyword, it indicates parallel INSERTs on the specified table. Currently, only parallel INSERTs following `INSERT INTO SELECT` are supported; parallelism can be specified for both INSERT and SELECT, or only one of them.

If any of the following conditions occur, the INSERT parallelism will not be effective, and the corresponding statement will generate a non-parallel execution plan.

- PARALLEL DML DISABLED: Only when PARALLEL DML is explicitly ENABLED in the session will the PARALLEL hint in the INSERT statement be effective.
- INSERT MULTI VALUES, multi-table INSERT, and INSERT ON DUPLICATE cannot be parallel.
- Lists, temporary tables, DBLinkS, and materialized view base tables are not allowed to be parallel.
- Tables with foreign key constraints, INSERT TRIGGERs, and LOB columns do not allow parallel.
- XA transactions do not allow parallel; once a local parallel transaction is started, a new XA transaction cannot be STARTed; after STARTing an XA transaction, parallelism will not be effective.
- Serializable transactions do not allow parallel.

***Example***

```sql
DROP TABLE IF EXISTS tab_parallel_insert_src PURGE;
CREATE TABLE tab_parallel_insert_src(id int, c1 varchar(1000));

INSERT INTO tab_parallel_insert_src VALUES(0, lpad('a', 1000, 'a'));
INSERT INTO tab_parallel_insert_src VALUES(1, lpad('a', 1000, 'a'));
COMMIT;

DROP TABLE IF EXISTS tab_parallel_insert_dst purge;
CREATE TABLE tab_parallel_insert_dst(id int, c1 varchar(1000));

-- Generating non-parallel plan
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
 -- Generating parallel INSERT execution plan
 
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

Parallel DML usage restrictions:

- When the table has been modified in parallel DML, it cannot be queried within the transaction (including SELECT/UPDATE/DELETE).
- When the table has executed DML, it cannot perform parallel DML on that table within the same transaction.
- After executing parallel INSERT:
  - If the table has an index, it cannot perform parallel INSERT again, nor non-parallel INSERT.
  - If the table does not have an index, it can perform parallel INSERT or non-parallel INSERT multiple times.

Parallel INSERT usage recommendations:

- Parallel INSERT is suitable for data import scenarios, with immediate commit after data import to avoid subsequent business impacts due to parallelism constraints.
- It is not recommended to insert data with indexes, as index conflicts are significant and greatly affect parallel performance.
- Parallel INSERT's degree of parallelism is subject to resource limitations; the degree should be properly set, and execution will align with the actual parallel resources requested.
- Parallel INSERT involves additional parallel resource application and allocation, data distribution operations, and is not recommended for small data amounts or insufficient resources.

***Example***

```sql
-- INSERT specifying parallel
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

### DP_MAX_JOIN_TABLES Hint Item

When using the DP_MAX_JOIN_TABLES hint item, it can control the number of hash join tables supported by the DP algorithm. If the number of tables joined in the SQL statement exceeds this value, it will utilize the greedy algorithm, which might lead to a non-optimal plan. If it is less than or equal to this value, the DP algorithm will be used, resulting in an optimal plan, but with a larger memory overhead. When configuring the DP_MAX_JOIN_TABLES hint item, it is necessary to specify the maximum number of tables supported by the DP algorithm [number of join tables], which will take effect for the entire SQL statement.

If the SQL statement specifies multiple DP algorithm-supported hash join tables via hints, the maximum value among these hints will take effect.

The maximum number of tables specified for the DP algorithm via hints is 32 and the minimum is 4; exceeding this range will use the hidden configuration parameter DP_MAX_JOIN_TABLES.

***Example***

```sql
-- By specifying different DP_MAX_JOIN_TABLES (4, 32) via hints, the maximum value of 32 will take effect.
select /*+ DP_MAX_JOIN_TABLES(4) */ *
  from  max_dp_join_table_1 t1 
left join max_dp_join_table_t2 t2 
  on t1.a = t2.a
left join (select /*+ DP_MAX_JOIN_TABLES(32) */ t3.a, t4.b from max_dp_join_table_4 t4
                    left join max_dp_join_table_3 t3
					on t4.a = t3.a) t5 
   on t5.a = t1.a;

-- When the specified value for DP_MAX_JOIN_TABLES exceeds the range, the hint will not take effect.
select /*+ DP_MAX_JOIN_TABLES(33) */ *
from  max_dp_join_table_1 t1 left join max_dp_join_table_t2 t2 on t1.a = t2.a;
```

If an error such as "no free block in sql main pool part 0" occurs when using DP_MAX_JOIN_TABLES due to insufficient memory, please reduce DP_MAX_JOIN_TABLES or refrain from using it.

<span id="leadinghint" name="leadinghint"></span>

### LEADING Hint Item

When using the LEADING hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer to access tables in the specified order during multi-table associations.

In hint_intersperse, only table names or aliases are supported for indicating order and are case insensitive.

The order of table access in the LEADING hint is an advisory order, not mandatory. If an unmodifiable JOIN type (OUTER) is encountered, the order will not change.

If the number of tables specified in hint_intersperse exceeds the actual number of JOINed tables, the optimizer will not accept this hint.

If invalid tables appear in hint_intersperse, the optimizer will not accept the order specified after that table, but will still accept the order specified before that table.

If multiple tables are specified in hint_intersperse, it indicates the prefix of the joining order, for example, LEADING(b a) indicates that the order (b a) c is compliant with the specified order, while (c (b a)) is not.

***Example***

```sql
SELECT /*+ LEADING(b e)*/ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department
AND b.branch_no = '0401'
AND d.deparment_no = '000';
```

<span id="nousehashhint" name="nousehashhint"></span>

### NO_USE_HASH Hint Item

When using the NO_USE_HASH hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer not to perform HASH JOIN during the associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ NO_USE_HASH(d) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="nousemergehint" name="nousemergehint"></span>

### NO_USE_MERGE Hint Item

When using the NO_USE_MERGE hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer not to perform MERGE JOIN during the associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ NO_USE_MERGE(d e) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="nousenlhint" name="nousenlhint"></span>

### NO_USE_NL Hint Item

When using the NO_USE_NL hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer not to perform NEST LOOPS during the associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ NO_USE_NL(a) */ *
FROM area a, branches b
WHERE a.area_no = b.area_no
AND a.area_no='04'
AND b.branch_no='0401';
```

<span id="usehashhint" name="usehashhint"></span>

### USE_HASH Hint Item

When using the USE_HASH hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer to perform HASH JOIN during associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ USE_HASH(d) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="usemergehint" name="usemergehint"></span>

### USE_MERGE Hint Item

When using the USE_MERGE hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer to perform MERGE JOIN during associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ USE_MERGE(d e) */ *
FROM branches b, department d, employees e
WHERE b.branch_no = e.branch
AND d.deparment_no = e.department;
```

<span id="usenlhint" name="usenlhint"></span>

### USE_NL Hint Item

When using the USE_NL hint item, it must also use hint_intersperse to specify the table name [table name], indicating the optimizer to perform NEST LOOPS during associations between the specified tables and other tables.

***Example***

```sql
SELECT /*+ USE_NL(a) */ *
FROM area a, branches b
WHERE a.area_no = b.area_no
AND a.area_no='04'
AND b.branch_no='0401';
```

<span id="selectivityhint" name="selectivityhint"></span>

### SELECTIVITY Hint Item

When using the SELECTIVITY hint item, the syntax differs from other hints. The SELECTIVITY hint can only follow the filter, specifying the selectivity of these filters, followed by a floating-point number between 0 and 1.

The selectivity of AND, OR, and BETWEEN cannot be specified through hints and can only be calculated.

***Example***

```sql
SELECT area_no
FROM area
WHERE area_no > 1 selectivity 0.8;
```


<span id="bulkload" name="bulkload"></span>

### BULKLOAD Hint Item

When using the BULKLOAD hint item, there is no need to specify the table name; this hint will take effect only in the scenario of single-table insertion in LSC tables, indicating that batch insert operations on LSC tables can reduce redo generation and improve insertion speed, but it has certain impacts on transaction visibility, as detailed in the following constraints.

***Example*** for LSC tables

```sql
INSERT /*+ BULKLOAD */ INTO area  VALUES ('06','EastChina','Shanghai');
```

When using the BULKLOAD hint to optimize inserts on tables, it guarantees that the inserted data is invisible to other transactions before the transaction commits; it does not guarantee visibility of the new inserted data to this insert transaction itself. After the transaction is committed, it ensures that the inserted data is visible to both this session and other transactions.

The related constraints for using the BULKLOAD hint are as follows:

- When auto-commit is enabled, using the BULKLOAD hint is prohibited; the BULKLOAD hint can be enabled by setting the parameter ENABLE_BULKLOAD_AUTO_COMMIT.


- The BULKLOAD hint cannot be used simultaneously with [SAVEPOINT](../SQL Statements/SAVEPOINT) functionality.
- The BULKLOAD hint cannot be used in [INSERT ON DUPLICATE UPDATE](../SQL Statements/INSERT) statements.
- Within the same transaction, after using the BULKLOAD hint for insertion, no other DML operations (except for insertion) are allowed on that table (but not for INSERT ON DUPLICATE UPDATE).
- Within the same transaction, the BULKLOAD hint can only be used for insertion on one table.
- Insertion operations using the BULKLOAD hint will block the forced conversion operation of LSC tables, specifically `ALTER TABLE ... STABLE/COMPACT`, until the BULKLOAD insertion transaction is committed.

The rollback mechanism in case of a transaction failure after using the BULKLOAD hint is as follows:

- Within the same transaction, if the current statement fails after executing the BULKLOAD insertion, the entire transaction will roll back and throw the corresponding error message. For example, if a multi-value insertion with the BULKLOAD hint is executed but some values are successfully inserted, if any of the later values fail, the entire transaction will roll back.
- After executing the insertion statement with the BULKLOAD hint within the same transaction, if the execution fails due to BULKLOAD hint constraints, syntax errors, verification errors, or partial resource allocation failures, the failed statement will roll back; if the failure is due to other reasons, the entire transaction will roll back and throw the corresponding error message.

***Example*** for LSC tables

```sql
-- After enabling auto-commit, using the BULKLOAD hint for insertion throws an error
SET AUTOCOMMIT ON;
INSERT /*+ BULKLOAD */ INTO area  VALUES ('07','EastChina','Shanghai');
YAS-03728 action not allowed : autocommit on do bulkload insert
   
-- After enabling auto-commit, set the parameter ENABLE_BULKLOAD_AUTO_COMMIT to open bulkload
ALTER SYSTEM SET ENABLE_BULKLOAD_AUTO_COMMIT=TRUE;
INSERT /*+ BULKLOAD */ INTO area  VALUES ('07','EastChina','Shanghai');
Succeed

SET AUTOCOMMIT OFF;
-- After using the BULKLOAD hint for insertion on the area table within a transaction, attempting to update the area table throws an error
INSERT /*+ BULKLOAD */ INTO area  VALUES ('08','EastChina','Shanghai');
UPDATE area SET area_name='SouthChina' WHERE area_no='02';
YAS-03728 action not allowed : bulkloading table update/delete/merge

-- At this point, only the UPDATE statement will roll back
COMMIT;
-- After committing the transaction, it can be seen that the data ('08','EastChina','Shanghai') has been successfully inserted into the area table
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           Shanghai       
02      WestChina           Chengdu        
03      SouthChina           Guangzhou      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  
06      EastChina           Shanghai
07      EastChina           Shanghai
08      EastChina           Shanghai
```

It is recommended to use the BULKLOAD hint for insertion in scenarios where queries and other DML operations are not required during the import period, such as incremental batch import.

<span id="deduplicate" name="deduplicate"></span>

### DEDUPLICATE Hint Item

When using the DEDUPLICATE hint item, there is no need to specify the table name; this hint only applies to single-table insertion or BULKLOAD imports for LSC tables and indicates that when batch inserting data with unique constraints or primary key constraints into LSC tables, if the inserted data conflicts with existing data, it will perform deduplication, replacing the rows corresponding to conflicting data with the newly inserted rows.

***Example*** for LSC tables

```sql
-- When attempting to insert duplicate data without using the DEDUPLICATE hint, violates the unique constraint
INSERT INTO area  VALUES ('05','SouthChina','Shenzhen');
YAS-02030 unique constraint violated
-- After using the DEDUPLICATE hint, the insertion of duplicate data succeeds, and querying the area table will show that the data with AREA_NO='05' has been replaced with the new inserted data
INSERT /*+ DEDUPLICATE */ INTO area  VALUES ('05','SouthChina','Shenzhen');
SELECT area_no,area_name,DHQ FROM area WHERE AREA_NO='05';
AREA_NO AREA_NAME        DHQ    
------- ---------------- -------------
05      SouthChina           Shenzhen  
```

<span id="max_workers_per_exec" name="max_workers_per_exec"></span>

### MAX_WORKERS_PER_EXEC Hint Item

When using the MAX_WORKERS_PER_EXEC hint item, there is no need to specify the table name. This hint applies only to TAC tables and LSC tables in ISC distributed databases for query and subquery statements, specifying the number of stages that can be executed simultaneously.

***Example*** for ISC Distributed Cluster Deployment LSC tables and TAC tables

```sql
SELECT /*+ max_workers_per_exec(2) */*
FROM orders_info o, sales_info s,finance_info f
WHERE o.salesperson = s.salsperson
AND s.branch = f.branch;
```
