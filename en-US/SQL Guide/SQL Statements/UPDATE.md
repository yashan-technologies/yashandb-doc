General Description
----

UPDATE is used to update data in the base table of a database table or view.

When updating data in the LSC table, the following constraints apply:

- Cross-partition updates on the LSC table are not allowed.
- UPDATE operations cannot be performed on AC.
- To update cold data in the LSC table, ROW MOVEMENT must be enabled. The update of cold data in the LSC table uses an optimistic locking mechanism, where lock information for the rows is retained in a buffer during the transaction, and rows are actually locked according to buffer information upon transaction commit. In case of concurrency, locking failures during transaction commits may cause transaction failures.
- If it is necessary to update the primary key/unique key of the LSC table, ensure that the new values of the primary key/unique key still satisfy uniqueness within the same UPDATE statement. It is recommended to avoid batch updating primary key/unique key data within the same statement to prevent unnecessary false alarms related to uniqueness checks caused by the data processing mechanism of the LSC table.
	
    For example, to increment the value of the primary key column area_no by 1 for all records in the LSC type area table, it is not recommended to execute `UPDATE area SET area_no = area_no + 1;` directly. Instead, it is advisable to start from the maximum value and execute an UPDATE statement for each key value to increment it individually, such as `UPDATE area SET area_no = area_no + 1 WHERE area_no = N;`.

When updating data based on a view for its base table, the following constraints apply:

- Not applicable for ISC Distributed Cluster Deployment.
- The target view must meet the following conditions:
    - All base tables of the view must be HEAP tables, and the view must not contain grouping, aggregation, deduplication, ROWNUM, or CONNECT BY operations.
    - The ROWID of data from the same base table must be unique in the view.
    - In multi-base table views, a base table must have columns that have equal connection conditions with all primary key or unique constraint columns of the other base tables to be able to UPDATE that table's data based on the view.
    - The base table of the view cannot be a remote table based on dblink.
- The columns in the UPDATE statement must correspond to the columns in the base table (cannot be other expression types).

By default, before the transaction (Transaction) of an UPDATE is committed (Commit), other sessions cannot query the updated data. You can enable auto-commit (SET AUTOCOMMIT ON) to allow other sessions to timely query the new data.

For a parent table defined with foreign key constraints to a child table, if the column to be updated is the foreign key item and its data value already exists in the child table, the corresponding data in the parent table cannot be updated.

Statement Definition
----

**update::=**

```ebnf+diagram
syntax::= UPDATE [hint] dml_table_expression_clause [t_alias] update_set_clause [WHERE condition] [returning_clause]
```

**[dml_table_expression_clause](#dmltableexpression)::=**

```ebnf+diagram
syntax::= [schema "."] (table_name [dblink] [partition_extension_clause] | view_name)
syntax::= ([schema "."] (table_name [dblink] [partition_extension_clause] | view_name)) | ( "(" (subquery) [subquery_restriction_clause] ")")
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**[subquery\_restriction\_clause](#subqueryrestrictionclause)::=**

```ebnf+diagram
syntax::= ((WITH) ((READ ONLY) | (CHECK OPTIION)) [CONSTRAINT constraint])
```

**[update\_set\_clause](#updatesetclause)::=**

```ebnf+diagram
syntax::= SET (column "=" (expr|DEFAULT|"(" subquery ")")|("("(column){"," (column)}")" "=""("(((expr)|DEFAULT|("(" subquery ")")){","((expr)|DEFAULT|("(" subquery ")"))}")"))){"," (column "=" (expr|DEFAULT|"(" subquery ")")|("("(column){"," (column)}")" "=""("(((expr)|DEFAULT|("(" subquery ")")){","((expr)|DEFAULT|("(" subquery ")"))}")")))}
```

**[returning_clause](#returningclause)::=**

```ebnf+diagram
syntax::= (RETURN|RETURNING) (expr) {"," (expr)} [BULK COLLECT ] INTO (variable) {"," (variable)}
```

### 1. hint

This statement is used to submit a given plan to the optimizer, allowing it to generate an execution plan for the statement accordingly. For details, refer to the relevant specification on [hint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/hint).

<span id="dmltableexpression" name="dmltableexpression" class="yaslink"></span>

### 2. dml\_table\_expression\_clause

This statement is used to specify the object to be updated, which can be a table name (including tables from local databases or [remote tables](../../All Manuals/Development Guide/SQL Reference Manual/通用SQL语法/dblink/dblink语法说明)), partition names of tables, view names, or subqueries, and an alias can be assigned to it.

For partitioned tables, if a partition object is not explicitly specified, the system determines the partition to be updated based on the values of partition item fields. When explicitly specified and the table is not defined as ENABLE ROW MOVEMENT:

*   When the partition table type is RANGE, if the partition item field of the table is updated, be careful not to go beyond the boundary of its corresponding partition, otherwise the update will fail with an error.
*   When the partition table type is LIST, if the partition item field of the table is updated, be sure that the data value is within the list items of its corresponding partition, otherwise the update will fail with an error.

For sharded tables in an ISC Distributed Cluster Deployment, modifying the values of its partition key fields is not allowed.

When the object to be updated is a subquery, the following constraints apply:

*   The base table of the column to be updated must satisfy the key preserved relationship, meaning that the result set of the subquery must uniquely map to a single row in the base table.
*   The column to be updated must come from the same base table.
*   The subquery cannot use operators or functions that result in non-unique outputs, including aggregate operators (group by, having, grouping sets), aggregate functions, window functions, distinct, limit offset/fetch n rows, sample, set operations, for update, and table functions.

***Example***1 for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range PARTITION (p_sales_info_range_1);
YEAR  MONTH BRANCH PRODUCT                QUANTITY                AMOUNT SALSPERSON  
----- ----- ------ --------- --------------------- --------------------- -------------
2001  01    0201   11001                        30                   500 0201010011 
2000  12    0102   11001                        20                   300       

-- The record to be updated is located in the sales_info_range table's p_sales_info_range_1 partition, whose boundary value is '2011'. Updating the year field to exceed the boundary will fail.
UPDATE sales_info_range PARTITION (p_sales_info_range_1) SET year='2023' WHERE year='2001';
YAS-02209 ROW MOVEMENT is not enabled

-- When not specifying a partition, it fails to update data as it doesn't match the current partition list.
UPDATE sales_info_list SET year='2021' WHERE year='2018';
YAS-02209 ROW MOVEMENT is not enabled
  
-- When specifying the partition, the original value and the update value are both within the specified partition. The update is successful.
UPDATE sales_info_list PARTITION (p_sales_info_list_1) SET year='2019' WHERE year='2018';
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_list PARTITION (p_sales_info_list_1);
YEAR  MONTH BRANCH PRODUCT                QUANTITY                AMOUNT SALSPERSON  
----- ----- ------ --------- --------------------- --------------------- -------------
2019  10    0101   11001                        20                   300
```

***Example***2 for ISC Distributed Cluster Deployment

```sql
-- sales_info_range is a range partitioned table, with the branch field as the partition key. Updating the partition key of a sharded table will fail.
UPDATE sales_info_range SET branch='0101' WHERE branch='0201';
YAS-04510 cannot update partitioning column for sharded table
```

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

#### 2.1. partition\_extension\_clause

Consistent with the description of partition_extension_clause in the [INSERT](INSERT) statement.

<span id="subqueryrestrictionclause" name="subqueryrestrictionclause" class="yaslink"></span>

#### 2.2. subquery\_restriction\_clause

Consistent with the description of subquery_restriction_clause in the [CREATE VIEW](CREATE VIEW) statement.

Can be omitted.

<span id="updatesetclause" name="updatesetclause" class="yaslink"></span>

### 3. update\_set\_clause

This statement is used to specify the columns to be updated and to assign values to them. Multiple updates to the same column are not allowed.

For UDT column fields, values are assigned through object initialization methods, as described in [User UDT](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/用户自定义类型).

YashanDB supports updating multiple column fields in two ways:

Method one: `UPDATE table_name SET column = value, column = value;`

Method two: `UPDATE table_name SET (column,...,column) = (value,...value);`

YashanDB supports assigning values to column fields with the following statements:

- [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) expressions
- Subqueries
- DEFAULT

When the result of the above statements is inconsistent with the data type defined for the column field, the system will first perform data type conversion, and an error will be returned on conversion failure.

When using method two to update multiple columns, the following constraint applies:

- Columns specified within parentheses must come from the same table; otherwise, an error will be returned.
- Except for cases where all specified columns are assigned DEFAULT values, the number of values must match the number of column fields.
- Subqueries and expressions must not appear simultaneously in the value.
- In the LSC table mode of ISC Distributed Cluster Deployment, using subqueries to assign values to column fields is currently not supported.

***Example*** for Heap tables and Standalone Deployment TAC tables

```sql
-- Insert data into the area table 
INSERT INTO area VALUES ('09','Other','Shanghai1');
  
-- Update multiple column fields (method one)
UPDATE area SET area_name=23*67,DHQ='Shanghai1' WHERE area_no='09';
  
--result 
SELECT area_no,area_name,DHQ FROM area WHERE area_no='09';
AREA_NO AREA_NAME      DHQ        
------- -------------- ------------
09      1541           Shanghai1

-- Update multiple column fields (method two)
UPDATE area SET (area_name,DHQ) = ('WestChina','Shaanxi') WHERE area_no='09';
```

When assigning values to column fields using a subquery, the updated columns must correspond in order with the items returned by the subquery, and the subquery result cannot yield multiple rows. If no results are returned by the subquery, NULL values are returned, and if the corresponding column has a NOT NULL constraint, the update will fail.

***Example***

```sql
-- Update the DHQ of the previously inserted record to 'Shanghai2' 
UPDATE area SET DHQ='Shanghai2' WHERE area_no='09';
  
-- No record with DHQ='Shanghai1' found in the area table, updating the DHQ field with NULL value triggers a NOT NULL constraint error
UPDATE area SET DHQ = (SELECT a.DHQ FROM area a WHERE a.DHQ='Shanghai1') WHERE area_no='09';
YAS-04006 cannot insert NULL value to column DHQ
  
-- Subquery returns multiple rows, update prompts an error 
UPDATE area SET DHQ = (SELECT a.DHQ FROM area a WHERE a.DHQ LIKE 'Shanghai%') WHERE area_no='09';
[1:24]YAS-04402 query expression return multiple rows
```

When using DEFAULT to assign values to column fields, if a DEFAULT value is defined for the corresponding column, the updated data will be that DEFAULT value; otherwise, it will be NULL. Based on this rule, if the corresponding column has a NOT NULL constraint, the update will fail.

***Example***

```sql
-- The DHQ field on the area table has a DEFAULT value defined as 'ShenZhen', hence updating with this value succeeds
UPDATE area SET DHQ=DEFAULT WHERE area_no='09';
  
-- The BRANCH_NAME field on the branches table is NOT NULL and has no DEFAULT value, hence updating with NULL fails
UPDATE branches SET branch_name=DEFAULT WHERE branch_no='0201';
YAS-04006 cannot insert NULL value to column BRANCH_NAME
```

### 4. WHERE condition

This statement is used to specify the [condition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/condition) to filter the records that will be updated. It can be omitted, which updates all rows in the table.

<span id="returningclause" name="returningclause" class="yaslink"></span>

### 5. returning\_clause

This statement indicates that a result set will be returned after the data is updated, and the result set will be assigned to specified variables. This statement can only be used in conjunction with [update_set_clause](#updatesetclause).

**expr**

Specifies the result columns, separated by `,`, where each column is a [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), but cannot be a sequence, pseudocolumn, aggregate function, window function, variable, or sub-query, and binding parameters are not allowed in the expression.

**variable**

The variables to which the results correspond, which can be a declared variable or a binding parameter depending on the statement used. If BULK COLLECT is used, the target object for INTO must be of collection type.

This statement can be used in procedural bodies or driver client programs; examples for the use of this statement as static SQL and dynamic SQL in procedural bodies are provided in the PL manual's [DML Statement](../../All Manuals/Development Guide/PL Reference Manual/PL Statements/DML Statement) and [EXECUTE Statement](../../All Manuals/Development Guide/PL Reference Manual/PL Statements/EXECUTE Statement).