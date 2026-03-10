## Introduction to Join Orders

Join is one of the most common features in SQL syntax, and it plays a critical role in slightly more complex business scenarios. In introductory knowledge, it has been generally stated that plans are combinations of various operators. For Join, there are often numerous combinations. This undoubtedly makes selecting the optimal Join combination exceptionally challenging.

Assuming there are four tables t1, t2, t3, and t4, and performing the following join query on them:

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

For the above SQL statement, even with just four layers of Join, considering the types of Join operators, the order of scanning the joined tables, and the types of scan operators, the operator combinations can reach dozens.

In fact, in the database field, Join Orders are essentially an NP hard problem. Therefore, optimizing complex Joins requires specific techniques to circumvent such a large solution space.

Before explaining how to perform Join optimization, it is essential to understand the corresponding algorithms for Join Orders. This helps in understanding how Joins are generated, thereby fostering both comprehension and insight.

Currently, YashanDB primarily employs two types of Join algorithms:

- Dynamic Programming (DP): For connections involving 8 or fewer tables, YashanDB uses the DP algorithm to determine Join Orders.
   
   The essence of the DP algorithm is a brute-force method where the basic approach for joining n tables gives a number k, with `1 < k < n`, where `best(1..n) = best(1..k) + best(k..n) + cost(join(1..k), join(k..n))`. This generates the optimal join order through looping or recursion.
   
   Therefore, under the DP algorithm and a correct cost model, it can always find the optimal Join plan. However, as the number of tables increases (beyond 8 tables), the complexity of the DP algorithm cannot support efficient generation of Join Orders.

- Greedy Operator Ordering (GOO): For connections involving more than 8 tables, YashanDB employs the GOO algorithm to determine Join Orders.

   The GOO algorithm finds the two nodes that yield the minimum join cost by combining the tables in pairs, then replaces the two with a new node and repeats this process until a Join Tree is formed. Consequently, the time complexity of the GOO algorithm is O(n^3), while the space complexity is O(n).

## Optimization of Complex Joins

Optimizing complex Joins needs to follow a general principle: Joins that can filter out a lot of data at a low cost should be executed first.

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

For Joins like T1 and T2 that can filter out a significant portion of data, they should be executed early during complex Join optimization. If these heavily filtering Joins are executed first, the number of rows that need to be processed later will decrease significantly, hence reducing the overall execution cost of the SQL statement.

Normally, if the number of joined tables is fewer than 8, the SQL engine's DP algorithm is already competent enough to select good Join Orders. However, when more than 8 tables are involved, due to the inherent limitations of the GOO algorithm, the Join Orders may not be optimal, leading to suboptimal SQL execution efficiency, requiring manual intervention to optimize such complex Joins.

For complex Join SQL, exhaustively testing all Join Orders is challenging. Therefore, attempts to optimize based on certain rules are necessary. General rules include:

- Selectivity Judgement: Evaluating whether the Rows estimates for each operator are accurate.
- Operator Selection Judgement: Assessing whether the operators selected are reasonable.

### Selectivity Judgement

If an operator has filtering conditions, it will apply a layer of filtering on the Rows produced. How much filtering is estimated is determined by the selectivity. If the estimate of selectivity is not accurate, the Rows estimation may deviate significantly, leading to a misjudgment in the cost model (the selected Join Orders may not be the optimal solution).

Therefore, in the actual optimization of Join Orders, the first step usually involves checking whether the selectivity judgement is accurate.

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

In the above example, the erroneous estimation of the number of remaining rows after the Join made T1 and T2 join first, leading to incorrect judgement of the Join Orders, and ultimately a poor plan. During actual optimization, YashanDB provides two capabilities to assess the accuracy of Rows estimation.

#### explain

As shown in the above example, by comparing the actual SELECT results with the values returned by EXPLAIN, one can assess the accuracy of Rows estimation. For specific usage, please refer to [EXPLAIN](../../../Development Guide/SQL Reference Manual/SQL Statements/EXPLAIN).

#### autotrace

Enabling autotrace directly can substitute for the above comparison process. For specific usage, please refer to [SET AUTOTRACE](../../../Development Guide/SQL Reference Manual/SQL Statements/SET AUTOTRACE).

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

### Operator Selection Judgement

Apart from selectivity judgement, the accuracy of the chosen operators is equally crucial; inappropriate operator selection can lead to severe efficiency degradation.

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

In the above examples, it is evident that selecting Nested Loops Inner incurs a greater cost than selecting Hash Join Inner. Each Join operator has its applicable scenarios; for instance, Joins between large tables may be more suitable for Hash Joins, while Joins on ordered columns are likely more suitable for Merge Joins.

In certain complex scenarios, issues similar to incorrect Join operator selection may easily arise. For example, not using an index for a Join between large tables and choosing Nested Loop Join instead of Hash Join, and creating the Hash table with a large table when opting for Hash Join. Thus, to evaluate operators, an understanding of the applicable scenarios for each operator is required.

### Hint Optimization

Combining the above two categories of issues, there are several hints that can adjust Join Orders.

- Join Hint
- Index Hint
- Selectivity Hint

#### Selectivity Adjustment

When issues arise related to selectivity, Selectivity Hint can be employed to make adjustments.

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

YashanDB can utilize Selectivity Hint to immediately adjust Join Orders. However, such adjustments may require a certain understanding of the processing logic related to selectivity. For more information about selectivity, please refer to [Selectivity and Statistics](Selectivity and Statistics).

#### Operator Adjustment

Operator adjustments are of a larger granularity and directly change the executed operators through hints. Before performing such operations, it is essential to understand the principles and advantages/disadvantages of each operator.

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

In the above example, if only the c1 column data is needed and no access to the base table is required, a hint can be used to specify index scan directly, thereby manually optimizing the plan. The same approach applies to other inappropriate operator usage scenarios, such as selecting a nested loop join instead of a hash join when it should have been a hash join.