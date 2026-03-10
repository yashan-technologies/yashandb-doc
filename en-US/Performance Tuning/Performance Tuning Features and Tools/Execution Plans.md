The execution plan is an important auxiliary tool in the SQL optimization process. It helps determine whether the optimizer has selected the optimal plan or whether a new index is effective.

An execution plan defines the order and algorithm by which the SQL engine runs a SQL statement. The execution plan can be queried in the *yasql* client using the [EXPLAIN](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/EXPLAIN) command or the [AUTOTRACE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/SET AUTOTRACE) command.

A typical execution plan is shown below:

```sql
EXPLAIN SELECT branch_no FROM area a, branches b WHERE a.area_no = b.area_no; 

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3244719443                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS INNER            |                      |            |    100000|      690( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | SYS        |    100000|      442( 0)|                                |
|  3 |   TABLE ACCESS BY INDEX ROWID  | AREA                 | SYS        |          |             |                                |
|* 4 |    INDEX UNIQUE SCAN           | SYS_C_18             | SYS        |         1|      149( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   4 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")        

```

The execution plan is essentially a binary tree structure. The executor executes the operators defined in the execution plan in post-order traversal. The output above is presented in a one-dimensional format, which is derived from the post-order traversal of the binary tree. For example, in this case, the execution sequence of the operators is 2->4->3->1->0.

**ID**

The unique identifier for an execution step, which does not indicate the execution order.

**Operation type**

The execution operator, with preceding spaces indicating the hierarchical relationship of the plan. Operators without lower-level plans, such as 2 and 4, indicate data retrieval from the database. These operators represent the access paths or techniques used to retrieve data.

The operator with ID 2 indicates a full table scan, retrieving all rows from the BRANCHES table.

The operator with ID 4 indicates searching for ROWID in the AREA table's index, where BRANCHES.AREA_NO is equal to it. For example, the ROWID value for AREA_ID '01' is 'AADShUAABAAAnPJAAK'.

The operator with ID 3 indicates that, based on the ROWID retrieved from level four, the AREA table is accessed to get the data row corresponding to that ROWID.

The operator with ID 1 indicates that the data retrieved from the BRANCHES and AREA tables is joined, and the result is returned to the operator with ID 0, using the NESTED LOOP JOIN algorithm.

In the parallel scenario of Standalone Deployment or in ISC Distributed Cluster Deployment, the PX (parallel execute) operator is used to implement data sending and receiving across nodes. For example, in the execution operator information for this statement in ISC Distributed Cluster Deployment, it is shown as follows:

```sql
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  DISTRIBUTED COORDINATOR       |                      |            |          |             |                                |
|  2 |   COL TO ROW                   |                      |            |          |             |                                |
|  3 |    PX N2I REMOTE               | QUEUE_0              |            |    100000|     1943( 0)|                                |
|* 4 |     HASH JOIN INNER            |                      |            |    100000|     1604( 0)|                                |
|* 5 |      PX N2N REMOTE             | QUEUE_1              |            |    100000|      606( 0)|                                |
|  6 |       TABLE ACCESS FULL        | AREA                 | SALES      |    100000|      442( 0)|                                |
|* 7 |      PX N2N REMOTE             | QUEUE_2              |            |    100000|      606( 0)|                                |
|  8 |       TABLE ACCESS FULL        | BRANCHES             | SALES      |    100000|      442( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
```

Here, 'PX N2I REMOTE' indicates that data from multiple DNs is gathered to the CN, while 'PX N2N REMOTE' indicates a hash redistribution of data from DN.

**Rows**

The estimated row count calculated by the optimizer based on statistics and specific algorithms, which usually does not accurately reflect the final execution calculated row count.

**Cost**

A reference value calculated by the optimizer based on operators and hardware information. A higher Cost value indicates that the plan at that layer consumes more resources.

**Partition info**

The range of partitions scanned by the operator.