General Description
----

DELETE is used to remove records from the base table of a database table or view.

When deleting data from the LSC table, the constraints are as follows:

-  The deletion of cold data from the LSC table uses an optimistic locking mechanism, wherein row lock information is retained in a buffer during the transaction, and locks are applied to the rows based on the buffer information upon transaction commit. In concurrent scenarios, transaction commits may fail to acquire locks, resulting in transaction failure.

When deleting records from the base table based on a view, the constraints are as follows:

- Not applicable for ISC Distributed Cluster Deployment.
- The target view must meet the following conditions:
	- All base tables of the view must be HEAP tables, and the view should not contain grouping, aggregation, de-duplication, ROWNUM, or CONNECT BY operations.
	- Data ROWIDs from the same base table in the view must be unique.
	- In multi-base table views, a base table must have column values that meet equality join conditions with the primary key or unique constraint columns of all other base tables to DELETE data from that table based on the view.
	- The base table of the view cannot be a remote table based on dblink.

By default, before the DELETE transaction is committed, other sessions can still query the deleted data. Automatic commit can be enabled (SET AUTOCOMMIT ON) to promptly prevent other sessions from accessing the deleted obsolete data.

For a parent table with foreign key constraints defined in the child table, if the value of the foreign key column to be deleted already exists in the child table, the row data of this parent table cannot be deleted (unless the foreign key constraint is defined with ON DELETE). For detailed descriptions, please refer to the general SQL syntax [constraint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/constraint (yashan Mode)) about FOREIGN KEY.

Statement Definition
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

This statement is used to propose a given strategy to the optimizer, enabling it to produce an execution plan for the statement according to this strategy. [See hint description](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/hint).

### 2. table\_reference

This statement is used to specify the object from which records will be deleted, which may include table names (including local database tables or [remote tables](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink)), partition names of tables, or view names.

When the deletion object is a subquery, the following constraints exist:

* The base table to be deleted must satisfy the key preserved relationship, meaning the result set of the subquery must uniquely correspond to a single row in the base table.
* If multiple base tables exist in the subquery that satisfy the key preserved relationship, one base table will be randomly selected for deletion.
* The subquery cannot use operators or actions that yield non-unique output, including aggregation operators (group by, having, grouping sets), aggregate functions, window functions, distinct, limit offset/fetch n rows, sample, set operations, for update, and table functions.

#### 2.1. dblink

This statement indicates that the table to be deleted is a remote table; for detailed explanation, see [dblink](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink).

#### 2.2. partition\_extension\_clause

This description is consistent with that in the [INSERT](INSERT) statement for partition_extension_clause.

If not specified in this statement, the system will determine the table partition where the records to be deleted reside based on the partition key field values.

When this statement is specified, the records to be deleted must exist in the specified partition; otherwise, deletion will not succeed.

#### 2.3. subquery\_restriction\_clause

This description is consistent with that in the [CREATE VIEW](CREATE VIEW) statement for subquery_restriction_clause.

It can be omitted.

#### 2.4. t\_alias

Defines an alias.

### 3. WHERE condition

This statement is used to specify the [condition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/condition), and records filtered according to this condition will be deleted. It can be omitted, which means all rows of the table will be deleted; however, the data space occupied by the table and its indexes will still be retained.

***Example***1

```sql
DELETE FROM area WHERE area_no='08';
```

***Example***2 for Standalone Deployment and YAC Deployment

```sql
-- The p_orders_info_1 partition of the orders_info table contains the following four records
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION (p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE                       SALESPERSON            ID 
----------------- ---------- ----- ------ -------------------------------- ------------- ----------- 
20010102020001    11001      02    0201   2022-05-01 22:55:32              0201010011            300
20210102020002    11001      02    0201   2022-05-01 22:55:32              0201008003            400
20210102020002    11002      02    0201   2022-05-01 22:55:32              0201010011            200
20210102020002    10001      02    0201   2022-05-01 22:55:32              0201008003            100
 
-- Deleting specified records from the specified partition
DELETE FROM orders_info PARTITION(p_orders_info_1) WHERE ID=300;
 
-- result
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION (p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE                       SALESPERSON            ID 
----------------- ---------- ----- ------ -------------------------------- ------------- ----------- 
20210102020002    11001      02    0201   2022-05-01 22:55:32              0201008003            400
20210102020002    11002      02    0201   2022-05-01 22:55:32              0201010011            200
20210102020002    10001      02    0201   2022-05-01 22:55:32              0201008003            100
```

***Example***2 for Heap tables

```sql
-- branches is a HEAP table, with a foreign key constraint defined on area_no field with CONSTRAINT c_branches_1 REFERENCES area(area_no) ON DELETE SET NULL

-- View records in the child table corresponding to area_no '04'
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='04';
BRANCH_NO BRANCH_NAME             AREA_NO ADDRESS          
--------- ----------------------- ------- -----------------
0401      Beijing                     04                         
0402      Tianjin                     04                         
0403      Dalian                     04      Dalian City             
0404      Shenyang                     04   
 
-- Delete from the parent table area 
DELETE FROM area WHERE area_no='04';
COMMIT;

-- Corresponding field values in the child table are set to NULL
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='04';
BRANCH_NO BRANCH_NAME                                                      AREA_NO ADDRESS                                                          
--------- ---------------------------------------------------------------- ------- ---------------------------------------------------------------- 
```
