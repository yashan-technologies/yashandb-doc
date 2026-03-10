## SQL and Execution Plans

An execution plan is a description of the entire process of executing an SQL statement. The quality of an execution plan largely determines the efficiency of SQL execution.

It is precisely because an SQL statement can be executed by different plans that SQL tuning becomes meaningful.

The following will explain the relationship between plans and tuning with examples, illustrating how plans impact performance.

## Plans and Operators

***Example***

```sql
EXPLAIN SELECT * FROM area WHERE area_no = 1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 3728302104
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  TABLE ACCESS FULL             | AREA                 | SALES      |         1|       13( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : filter("AREA"."AREA_NO" = 1)
```

For the table that needs to be queried, there can be multiple scanning methods to retrieve data from the table, and these different scanning methods are different scan operators.

The existence of different operators allows for different execution plans for an SQL statement, and the most suitable and efficient operator for different SQL statements may also vary.

The ultimate goal of SQL tuning is to find the most efficient one among these different execution plans.

Returning to the example above, when performing a simple scan on the table, it cannot be generalized which operator is optimal. A common misconception is that an index scan should be chosen regardless of the scanning scenario, which arises from a superficial understanding of what happens during the execution of operators. In fact, a full table scan is necessary when returning to the table, while an index scan is more efficient when there is no need to return to the table.

Therefore, a deep understanding of operators is required before performing tuning.

## Operator Efficiency Evaluation

Since different operators have different efficiencies, a standard is needed to evaluate the performance of each operator. This standard is represented by the cost displayed in the execution plan.

***Example***

```sql
EXPLAIN SELECT area_no FROM area WHERE area_no = 1;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 1386453710
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  INDEX FAST FULL SCAN          | SYS_C_33             | SALES      |         1|        6( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : filter("AREA"."AREA_NO" = 1)
```

From this example, it is easy to see that as mentioned in the previous section, when there is no need to return to the table, the cost of using an index scan is less than that of a full table scan.

A model that accurately reflects the real cost of operators is key to generating efficient plans. However, the execution cost model may fail in some more complex statements. In such cases, manual intervention may be needed to determine if certain operators' cost evaluations are inaccurate, and appropriate adjustments should be made to avoid poor plans, such as using hints to force a specific operator.

## Operator Combinations

Plans are often not just simple layers as shown in the above examples. When SQL becomes complex, it is necessary to consider how to choose combinations of multiple operators in addition to different operators.

***Example***

```sql
EXPLAIN SELECT * FROM area a, branches b WHERE a.area_no = b.area_no;

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 1557742453
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS INNER            |                      |            |    100000|      211( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | SALES      |    100000|      132( 0)|                                |
|  3 |   TABLE ACCESS BY INDEX ROWID  | AREA                 | SALES      |          |             |                                |
|* 4 |    INDEX UNIQUE SCAN           | SYS_C_33             | SALES      |         1|       13( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   4 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")
```

The best example of discussing operator combinations is Join. For a join, it is insufficient to consider only the lower-level scan operators; it is also necessary to consider the position of different tables in the join operator (left or right). For example, in a hash join, it is recommended to place the smaller table on the right (the build table) to lower the building cost.

Choosing the optimal operator combination is as crucial as selecting the optimal operator, and identifying the best combination is one of the most challenging aspects of tuning. Detailed tuning rules can be referenced in [Principles and Rules of SQL Tuning](../Principle and Rules of SQL Tuning/00Principle and Rules of SQL Tuning).