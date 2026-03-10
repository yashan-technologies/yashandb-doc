Hint is a special type of comment that appears in a fixed format and position within the SQL statement text, used to influence the optimizer's choice of execution plan. However, this influence is not mandatory; the optimizer may ignore SQL hint instructions in certain situations.

For related syntax about hints, please refer to [hint](../../../Development Guide/SQL Reference Manual/General SQL Syntax/hint).

## Hints for Changing Join Order

The LEADING syntax can change the order of the tables in the Join. A common practice is to adjust the size of the tables using LEADING to reduce the number of scans; or to adjust the order of table joins using LEADING to increase the selectivity and reduce the execution burden.

LEADING has certain limitations, such as only being able to change the order of specific tables, and cannot adjust the order between result sets that are produced from the joining of tables.

## Hints for Changing Join Type

USE_ and NO_USE_ syntax can specify the join type between two tables:

|Mode |Description |Remarks |
| :----------- | :---------------------------- | :------------------------------------------------- |
| USE_NL       | Prefer Nested Loop Join        | Any join type can be converted to a Nested Loop Join|
| NO_USE_NL    | Prefer non-Nested Loop Join    | Does not take effect if other join types cannot be generated|
| USE_HASH     | Prefer Hash Join               | Does not take effect if Hash Join cannot be used   |
| NO_USE_HASH  | Do not use Hash Join           | Uses Nested Loop Join or Merge Join based on cost assessment|
| USE_MERGE    | Prefer Merge Join              | Does not take effect if Merge Join cannot be generated|
| NO_USE_MERGE | Do not use Merge Join          | Uses Nested Loop Join or Hash Join based on assessment|

## Hints for Changing Access Path

Hint instructions can also change the access path, for example, by forcing an index scan or enforcing a full table scan.

|Mode |Description |Remarks |
| :----------- | :--------------------- | :--------------------------------------------------------- |
| INDEX        | Prefer the specified index     | Will always take effect, selecting the one with the lowest cost between Fast Full Scan and Index Scan |
| NO_INDEX     | Do not use the specified index | Will always take effect, selecting Table Full Scan       |
| INDEX_FFS    | Prefer Fast Full Scan          | Will always take effect, selecting only Fast Full Scan   |
| NO_INDEX_FFS | Do not use Fast Full Scan      | Will always take effect, allowing options like Index Range Scan|
| FULL         | Use full table scan            | Will always take effect, selecting Table Full Scan       |

## Hints for Parallel


When using the PARALLEL hint item, it allows for parallel querying. For syntax compatibility, when configuring the PARALLEL hint, it is necessary to use hint_intersperse to specify both the table name and the degree of parallelism [table name, degree], which still indicates the overall parallelism of the query.

If multiple tables in the query use hints to specify different degrees of parallelism, all tables will adopt the maximum specified value.

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


Please refer to [hint Reference](../../../Development Guide/SQL Reference Manual/General SQL Syntax/hint) for more details of Parallel DML and Parallel INSERT.

## Hints for Batch Mode


When using the BATCH_MODE hint item, there is no need to specify the table name; this hint applies only to queries and insert statements of HEAP tables in Standalone Deployment and is used to specify the batch execution mode of the current statement.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT /*+ BATCH_MODE(2) */*
FROM orders_info o, sales_info s,finance_info f
WHERE o.salesperson = s.salsperson
AND s.branch = f.branch;
```
