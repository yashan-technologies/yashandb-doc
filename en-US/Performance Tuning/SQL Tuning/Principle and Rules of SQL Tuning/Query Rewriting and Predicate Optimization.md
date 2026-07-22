## Redundant Query Rewriting

Consider the following statement:

```sql
SELECT AREA_NO FROM (SELECT * FROM AREA);
```

From the content of the statement, it can be seen that the target of this SQL statement is to obtain the AREA_NO column from the AREA table, but there is an extra redundant subquery, which will cause the SQL engine to execute the SQL and query out unnecessary projection columns.

This redundant writing increases the burden on the executor. Therefore, YashanDB will rewrite the above statement into a similar structure as follows:

```sql
SELECT AREA_NO FROM AREA;
```

## "IN / NOT IN + Subquery" Rewriting

For statements using "IN + subquery," when the query does not include AGGR, GROUP BY, CONNECT BY, or WINDOW FUNCTION, YashanDB will rewrite it into a semi-join or anti-join. This is because if executed according to the original SQL semantics, a complete execution of the AREA subquery is required each time data is fetched from the BRANCHES table. The rewritten statement will significantly reduce the number of scans on the AREA table, thus improving performance.

***Example***

```sql
explain select * from branches b where b.area_no in (select area_no from area);

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 2158759181
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS SEMI             |                      |            |       101|      179( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | REGRESS    |    100000|      132( 0)|                                |
|* 3 |   INDEX UNIQUE SCAN            | SYS_C_17             | REGRESS    |         1|        6( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   3 - Predicate : access("AREA"."AREA_NO" = "B"."AREA_NO")

16 rows fetched.
       
explain select * from branches b where b.area_no not in (select area_no from area);

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 708164047
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  NESTED LOOPS ANTI             |                      |            |         1|  1463780( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | REGRESS    |    100000|      132( 0)|                                |
|  3 |   INDEX FAST FULL SCAN         | SYS_C_17             | REGRESS    |    100000|       92( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Predicate : filter(INVERT("B"."AREA_NO" <> "AREA"."AREA_NO"))

16 rows fetched.
```

## "EXISTS / NOT EXISTS + Subquery" Rewriting

Similar to "IN / NOT IN + subquery," when the query does not include AGGR, GROUP BY, CONNECT BY, or WINDOW FUNCTION, the combination of "EXISTS / NOT EXISTS + subquery" may also be rewritten into a semi-join or anti-join.

***Example***

```sql
explain select * from branches b where exists (select area_no from area a where b.area_no = a.area_no);

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 3585099968
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS SEMI             |                      |            |       101|      179( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | REGRESS    |    100000|      132( 0)|                                |
|* 3 |   INDEX UNIQUE SCAN            | SYS_C_23             | REGRESS    |         1|        6( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   3 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")

16 rows fetched.
       
explain select * from branches b where not exists (select area_no from area a where b.area_no = a.area_no);

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 3371762311
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS ANTI             |                      |            |     99900|      179( 0)|                                |
|  2 |   TABLE ACCESS FULL            | BRANCHES             | REGRESS    |    100000|      132( 0)|                                |
|* 3 |   INDEX UNIQUE SCAN            | SYS_C_23             | REGRESS    |         1|        6( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   3 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")

16 rows fetched.
```

## "ANY/ALL + Subquery" Rewriting

When the right side of the comparison operator (>, >=, <, <=) is any/all, the any subquery will be rewritten into a semi-join, while the all subquery will be rewritten into an anti-join.

***Example***

```sql
explain select product from sales_info_hash s where amount > any(select revenue_total from finance_info f where s.branch = f.branch);

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 777020315                                       
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  HASH JOIN SEMI                |                      |            |      2660|     2056( 0)|                                |
|  2 |   PART SCAN ALL                |                      |            |    100000|      442( 0)| [0,1]                          |
|  3 |    TABLE ACCESS FULL           | SALES_INFO_HASH      | SYS        |    100000|      442( 0)|                                |
|  4 |   TABLE ACCESS FULL            | FINANCE_INFO         | SYS        |    100000|      442( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Predicate : access("S"."BRANCH" = "F"."BRANCH")          
                   filter("S"."AMOUNT" > "F"."REVENUE_TOTAL")   

18 rows fetched.

explain select product from sales_info_hash where amount > all(select revenue_total from finance_info);

PLAN_DESCRIPTION
---------------------------------------------------------------- 
SQL hash value: 3243986469                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|* 1 |  NESTED LOOPS ANTI             |                      |            |         1|   664389( 0)|                                |
|  2 |   PART SCAN ALL                |                      |            |    100000|      442( 0)| [0,1]                          |
|  3 |    TABLE ACCESS FULL           | SALES_INFO_HASH      | SYS        |    100000|      442( 0)|                                |
|  4 |   VIEW                         |                      |            |    100000|      447( 0)|                                |
|  5 |    TABLE ACCESS FULL           | FINANCE_INFO         | SYS        |    100000|      442( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Predicate : filter(INVERT("SALES_INFO_HASH"."AMOUNT" > "VSQ$1@SEL$0"."REVENUE_TOTAL"))

18 rows fetched.
```

## Predicate Related Processing

### Predicate Combination Optimization

Predicate reorganization includes two types: merging and expanding:

- Predicate merging removes redundant predicate conditions.

  Multiple redundant predicates are combined into one. For example, the predicate `A = B AND A != B` is merged into `always FALSE`.

- Predicate expansion constructs equivalent and efficient predicate conditions based on relational algebra formulas.

  New predicates are generated based on existing conditions. For example, `A > B AND B > C` can infer the condition `A > C`, which can, in some scenarios, transform a Cartesian join into an inner join.

### Predicate Pushdown

- Predicate pushdown refers to placing predicates in the most efficient place without changing their semantics. Clearly, the earlier a predicate is applied, the higher the efficiency.

- "Adapting to local conditions" involves reasonably placing predicates, such as putting index-related predicates on the index. This allows the storage engine to reduce the size of the result set returned based on that predicate, which is better than letting the storage engine return the entire index and having the SQL engine filter the result set.