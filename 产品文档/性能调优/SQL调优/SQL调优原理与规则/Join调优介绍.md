## Join Orders介绍

Join是SQL语法中最常见的一个特性，只要稍微复杂一点的业务场景都会用到Join。在入门知识中，已经大体介绍了计划是各个算子排列组合成的。对于Join来说，常常会有非常多的组合方式。这无疑使得选出一个最优的Join组合变得异常困难。

假设有t1、t2、t3、t4四张表，对它们执行如下join查询：

```sql
EXPLAIN SELECT * FROM t1, t2, t3, t4  WHERE t1.c1 = t2.c1 AND t2.c1 = t3.c1 AND t4.c1 = t2.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 745528657
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN INNER               |                      |            |         1|     2137( 0)|                                |
|  2 |   JOIN FILTER USE              |                      |            |    100000|      442( 0)|                                |
|* 3 |    TABLE ACCESS FULL           | T4                   | REGRESS    |    100000|      442( 0)|                                |
|* 4 |   JOIN FILTER CREATE           |                      |            |         1|     1499( 0)|                                |
|* 5 |    MERGE JOIN INNER            |                      |            |         1|     1499( 0)|                                |
|  6 |     MERGE SORT                 |                      |            |          |             |                                |
|* 7 |      HASH JOIN INNER           |                      |            |         3|     1068( 0)|                                |
|  8 |       TABLE ACCESS FULL        | T1                   | REGRESS    |    100000|      442( 0)|                                |
|  9 |       TABLE ACCESS FULL        | T2                   | REGRESS    |         3|      431( 0)|                                |
| 10 |     MERGE SORT                 |                      |            |          |             |                                |
| 11 |      TABLE ACCESS FULL         | T3                   | REGRESS    |         3|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access("T4"."C1" = "T2"."C1")
   3 - Predicate : RUNTIME FILTER(RUNTIME USE(0): "T4"."C1")
   4 - Predicate : RUNTIME FILTER(RUNTIME CREATE(0): "T2"."C1")
   5 - Predicate : access( "T1"."C1" = "T3"."C1" )
   7 - Predicate : access("T1"."C1" = "T2"."C1")
```

对于上述SQL语句来说，即使仅4层Join，在考虑Join算子种类、Join内Scan表的左右顺序的和Scan算子的种类的情况后，算子组合已高达几十种。

实际上，在数据库领域中的Join Orders本质就是一个NP hard难题。因此对于调优复杂Join来说，需要一些特定的技巧来规避如此大的解空间。

但在实际讲解如何进行Join调优之前，有必要对Join Orders相应的算法进行了解。这能有助于理解Join的生成方式，从而做到知其然，也知其所以然。

目前YashanDB中主要有两种Join算法：

- 动态规划（DP，Dynamic Programming）：小于等于8张表连接时，YashanDB会采用DP算法确定Join Orders。
   
   DP算法本质上是一种穷举法，基本思路为n张表join时，可以给定一个数字k，`1 < k < n`，`best(1..n) = best(1..k) + best(k..n) + cost(join(1..k), join(k..n))`，通过循环或递归实现最佳join order的生成。
   
   因此在DP算法和正确的cost model下，一定能够找最优的Join计划。但随着表的数量增加（超过8张表以上），DP算法的复杂度无法支撑高效的Join Orders生成。

- 贪心算法（GOO，Greedy Operator Ordering）：超过8张表连接时，YashanDB会采用GOO算法确定Join Orders。

   GOO算法对于n个节点所组成的集合，会先通过两两组合找到join cost最小的两个节点，然后将两者join作为新的节点（代替原来的两个节点）；重复此过程直至得到一个Join Tree。由此可得，GOO算法的时间复杂度是O(n^3)，空间复杂度为O(n)。

## 复杂Join的调优

复杂Join的调优需要遵循一个大体原则：能够低成本过滤掉大量数据的Join优先执行。

```sql
EXPLAIN SELECT * FROM t1, t2, t3, t4  WHERE t1.c1 = t2.c1 AND t2.c1 = t3.c1 AND t4.c1 = t2.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 3320065567
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  MERGE JOIN INNER              |                      |            |         4|     1724( 0)|                                |
|  2 |   MERGE SORT                   |                      |            |          |             |                                |
|* 3 |    MERGE JOIN INNER            |                      |            |         1|     1293( 0)|                                |
|  4 |     MERGE SORT                 |                      |            |          |             |                                |
|* 5 |      HASH JOIN INNER           |                      |            |         1|      862( 0)|                                |
|  6 |       TABLE ACCESS FULL        | T1                   | REGRESS    |        79|      431( 0)|                                |
|  7 |       TABLE ACCESS FULL        | T2                   | REGRESS    |         3|      431( 0)|                                |
|  8 |     MERGE SORT                 |                      |            |          |             |                                |
|  9 |      TABLE ACCESS FULL         | T3                   | REGRESS    |         3|      431( 0)|                                |
| 10 |   MERGE SORT                   |                      |            |          |             |                                |
| 11 |    TABLE ACCESS FULL           | T4                   | REGRESS    |        13|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access( "T2"."C1" = "T4"."C1" )
   3 - Predicate : access( "T1"."C1" = "T3"."C1" )
   5 - Predicate : access("T1"."C1" = "T2"."C1")
```

对于T1、T2这种能够提前过滤掉大部分数据的Join，在复杂Join调优时应让其提前。若先执行了这类过滤性强的Join，后续需要执行的条数将大幅降低，从而可以降低整体SQL的执行成本。

正常来说，如果连接表数量低于8，SQL引擎本身的DP算法已经足够优秀去选出较好的Join Orders。而在连接表数量超过8时，由于GOO算法本身的不足，Join Orders可能会出现不够优秀的问题，导致SQL执行效率不理想，此时需要人工介入对这种复杂Join进行调优。

对于复杂Join的SQL来说，穷举所有Join Orders的情况进行调优是比较困难的。因此需要基于某些规则进行调优的尝试。大体的规则如下：

- 选择率判断：判断各个算子的Rows评估是否准确。

- 算子选择判断：判断各个算子的选择是否合理。

### 选择率判断

如果算子上存在filter，会对算子生成的rows进行一层过滤，过滤多少的预估由选择率实现。如果选择率的估计不够准确，会导致rows评估偏差较大，进而导致cost model产生误判（选出的Join Orders可能不是最优解）。

因此，在实际调优Join Orders中，第一步通常是查看选择率的判断是否准确。

```sql
EXPLAIN SELECT * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 SELECTIVITY 0.0001 AND t2.c1 = t3.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 234816895
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  MERGE JOIN INNER              |                      |            |         1|     1293( 0)|                                |
|  2 |   MERGE SORT                   |                      |            |          |             |                                |
|* 3 |    HASH JOIN INNER             |                      |            |         1|      862( 0)|                                |
|  4 |     TABLE ACCESS FULL          | T1                   | REGRESS    |        79|      431( 0)|                                |
|  5 |     TABLE ACCESS FULL          | T2                   | REGRESS    |         3|      431( 0)|                                |
|  6 |   MERGE SORT                   |                      |            |          |             |                                |
|  7 |    TABLE ACCESS FULL           | T3                   | REGRESS    |         3|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access( "T1"."C1" = "T3"."C1" )
   3 - Predicate : access("T1"."C1" = "T2"."C1")

SELECT COUNT(*) FROM t1, t2 WHERE t1.c1 = t2.c1 SELECTIVITY 0.0001;

COUNT(*)
---------------------
                   79
```

在上述例子中，由于错误地估计了Join后的剩余条数，使得T1和T2先进行了Join，导致Join Orders的判断错误，最终计划不佳。在实际调优时，YashanDB提供了两个能力判断Rows的评估是否准确。

#### explain

如上述例子所示，通过比较实际select出来的值与explain出来的值判断rows评估是否准确，具体使用请查阅[EXPLAIN](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/EXPLAIN)。

#### autotrace

直接开启autotrace可以替代上述比较过程，具体使用请查阅[SET AUTOTRACE](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/SET AUTOTRACE)。

```sql
SELECT * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 SELECTIVITY 0.0001 AND t2.c1 = t3.c1;

Execution Plan
----------------------------------------------------------------
SQL hash value: 234816895
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | E - Rows | A - Rows | Cost(%CPU)  | A - Time | Loops    | Memory   | Disk     | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |        79|             |       354|        79|          |          |                                |
|* 1 |  MERGE JOIN INNER              |                      |            |         1|        79|     1293( 0)|       341|        79|          |          |                                |
|  2 |   MERGE SORT                   |                      |            |          |        79|             |       242|        79|          |          |                                |
|* 3 |    HASH JOIN INNER             |                      |            |         1|        79|      862( 0)|       188|        79|          |          |                                |
|  4 |     TABLE ACCESS FULL          | T1                   | REGRESS    |        79|        79|      431( 0)|        24|        79|          |          |                                |
|  5 |     TABLE ACCESS FULL          | T2                   | REGRESS    |         3|         3|      431( 0)|        53|         3|          |          |                                |
|  6 |   MERGE SORT                   |                      |            |          |        59|             |        42|        59|          |          |                                |
|  7 |    TABLE ACCESS FULL           | T3                   | REGRESS    |         3|         3|      431( 0)|        26|         3|          |          |                                |
+----+--------------------------------+----------------------+------------+----------+----------+-------------+----------+----------+----------+----------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access( "T1"."C1" = "T3"."C1" )
   3 - Execution : [BUILD] Memory : 327680  [BUILD] Time : 133  [HDT] RehashTimes : 0  [HDT] OriginPrime : 131071
       Predicate : access("T1"."C1" = "T2"."C1")
```

### 算子选择判断

除了选择率判断之外，选择的算子是否准确也至关重要，算子选择不恰当也会引起严重的效率下降。

```sql
EXPLAIN SELECT /*+LEADING(t1,t2) USE_HASH(t1,t2)*/ * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 AND t2.c1 = t3.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 4196032415
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN INNER               |                      |            |   1651424|     4545( 0)|                                |
|* 2 |   HASH JOIN INNER              |                      |            |   1651424|      885( 0)|                                |
|  3 |    TABLE ACCESS FULL           | T1                   | REGRESS    |      2528|      431( 0)|                                |
|  4 |    TABLE ACCESS FULL           | T2                   | REGRESS    |      1899|      431( 0)|                                |
|  5 |   TABLE ACCESS FULL            | T3                   | REGRESS    |         3|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access("T1"."C1" = "T3"."C1")
   2 - Predicate : access("T1"."C1" = "T2"."C1")


EXPLAIN SELECT /*+LEADING(t1,t2) USE_NL(t1)*/ * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 AND t2.c1 = t3.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 462255180
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN INNER               |                      |            |   1651424|     4848( 0)|                                |
|* 2 |   NESTED LOOPS INNER           |                      |            |   1651424|     1189( 0)|                                |
|  3 |    TABLE ACCESS FULL           | T1                   | REGRESS    |      2528|      431( 0)|                                |
|  4 |    TABLE ACCESS FULL           | T2                   | REGRESS    |      1899|      431( 0)|                                |
|  5 |   TABLE ACCESS FULL            | T3                   | REGRESS    |         3|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access("T1"."C1" = "T3"."C1")
   2 - Predicate : filter("T1"."C1" = "T2"."C1")

```

在上述示例中，可以发现选择Nested Loops Inner比选择Hash Join Inner代价更大。Join相关算子均有各自的适用场景，例如，大表之间的Join可能更适合选择Hash Join，如果两个表的Join列都是有序的可能更适合选择Merge Join。

在一些复杂的场景中，很容易出现类似Join相关算子选择错误的问题。例如在大表Join时没有索引的情况下选择了Nest Loop Join而未选择Hash Join，以及选择Hash Join时采用了大表进行Hash表的创建。因此如果要对算子进行判断，需要了解各个算子相关的适用场景。

### hint调优

结合上述的两类问题，主要有以下hint可以对Join Orders进行调节。

- Join Hint
- Index Hint
- Selectivity Hint

#### 选择率调整

上述出现选择率问题时，可以通过Selectivity Hint进行调整。

```sql
EXPLAIN SELECT * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 SELECTIVITY 0.0001 AND t2.c1 = t3.c1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 234816895
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN INNER               |                      |            |       480|     1317( 0)|                                |
|* 2 |   HASH JOIN INNER              |                      |            |       480|      885( 0)|                                |
|  3 |    TABLE ACCESS FULL           | T1                   | REGRESS    |      2528|      431( 0)|                                |
|  4 |    TABLE ACCESS FULL           | T2                   | REGRESS    |      1899|      431( 0)|                                |
|  5 |   TABLE ACCESS FULL            | T3                   | REGRESS    |         3|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access("T1"."C1" = "T3"."C1")
   2 - Predicate : access("T1"."C1" = "T2"."C1")


EXPLAIN SELECT * FROM t1, t2, t3  WHERE t1.c1 = t2.c1 AND t2.c1 = t3.c1 SELECTIVITY 1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 2568121636
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN INNER               |                      |            |   1651424|     1321( 0)|                                |
|* 2 |   HASH JOIN INNER              |                      |            |      2528|      866( 0)|                                |
|  3 |    TABLE ACCESS FULL           | T1                   | REGRESS    |      2528|      431( 0)|                                |
|  4 |    TABLE ACCESS FULL           | T3                   | REGRESS    |         3|      431( 0)|                                |
|  5 |   TABLE ACCESS FULL            | T2                   | REGRESS    |      1899|      431( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : access("T1"."C1" = "T2"."C1")
   2 - Predicate : access("T1"."C1" = "T3"."C1")

```

YashanDB通过Selectivity Hint的方式可以立竿见影地完成对Join Orders的调整。但这类的调整由于粒度较小，需要对选择率相关的处理逻辑有一定的了解，选择率相关介绍请查阅[选择率与统计信息](./选择率与统计信息)。

#### 算子调整

算子调整的粒度更大，直接通过hint的方式将执行的算子进行改变。在进行这类操作之前，需要对各个算子的原理以及优劣了解清楚。

```sql
EXPLAIN SELECT /*+INDEX(t1)*/ c1 FROM t1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 1102139983
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  COL TO ROW                    |                      |            |          |             |                                |
|  2 |   INDEX FAST FULL SCAN         | C1_T1                | REGRESS    |         8|      142( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Projection: RemoteTable[1][INTEGER]
   2 - Projection: Tuple[0, 0][INTEGER]

```

上述例子中，只需要取c1列数据不需要回表，可以直接使用hint指定采用index scan，从而手动使得计划最优。对于其他的算子使用不恰当场景也是同理，例如在选错成nested loop join时，可以使用Hint指定使用hash join进行规避。