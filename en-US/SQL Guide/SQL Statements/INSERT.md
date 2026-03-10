## General Description

INSERT is used to insert data into the base table of a table or view in the database.

In Standalone Deployment, it supports inserting data into a single table at a time, as well as inserting data into multiple tables simultaneously.

When inserting data into the base table through a view, the following constraints apply:

- Not applicable for ISC Distributed Cluster Deployment.
- The target view must meet the following conditions:
    - All base tables of the view must be HEAP tables, and the view must not contain grouping, aggregation, deduplication, ROWNUM, or CONNECT BY operations.
    - The ROWID of data from the same base table in the view must be unique.
    - For views with multiple base tables, a column in one base table must have an equi-join condition with the primary key constraint or unique constraint columns of all other base tables to allow INSERTing data based on the view.
    - The base table of the view cannot be a remote table based on dblink.
- The columns of the INSERT statement must correspond to the columns of the base table (cannot be of other expression types).
- When inserting multiple columns at the same time, those columns must come from the same base table.
- The INSERT ALL statement cannot operate on its base table based on a view; it can only operate directly on the table.
- The INSERT statement that includes ON DUPLICATE KEY UPDATE cannot operate on its base table based on a view; it can only operate directly on the table.

By default, before the INSERT transaction is committed, other sessions cannot query the newly inserted data. Auto-commit (SET AUTOCOMMIT ON) can be enabled as needed to allow other sessions to query the new data in a timely manner.

## Statement Definition

**insert::=**

```ebnf+diagram
syntax::= INSERT [hint] (single_table_insert|multi_table_insert)
```

**[single\_table\_insert](#singletableinsert)::=**

```ebnf+diagram
syntax::= single_insert_into_clause (insert_values_clause|subquery) [on_duplicate_clause|returning_clause]
```

**[single\_insert\_into\_clause](#singleinsertintoclause)::=**

```ebnf+diagram
syntax::= INTO table_reference [t_alias] [("(" column_name ")") {"," ("(" column_name ")")}]
```

**[table\_reference](#tablereference)::=**

```ebnf+diagram
syntax::= [schema "." ] (table_name [dblink] [partition_extension_clause] | view_name)
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**[insert\_values\_clause](#insertvaluesclause)::=**

```ebnf+diagram
syntax::= VALUES ("(" (expr|DEFAULT|udt_expr) {","(expr|DEFAULT|udt_expr)} ")")
{"," ("(" (expr|DEFAULT|udt_expr) {","(expr|DEFAULT|udt_expr)} ")")}
```

**[on\_duplicate\_clause](#onduplicateclause)::=**

```ebnf+diagram
syntax::= ON DUPLICATE KEY UPDATE (set_clause) {"," (set_clause)}
```

**[set\_clause](#setclause)::=**

```ebnf+diagram
syntax::= column_name "=" (DEFAULT|expr|udt_expr|VALUES(column_name_1))
```

**[returning_clause](#returningclause)::=**

```ebnf+diagram
syntax::= (RETURN|RETURNING) (expr) {"," (expr)} INTO (variable) {"," (variable)}
```

**[multi\_table\_insert](#multitableinsert)::=**

```ebnf+diagram
syntax::= (ALL (multi_insert_into_clause [insert_values_clause])
{" " (multi_insert_into_clause [insert_values_clause])} | conditional_insert_clause) subquery
```

**[multi\_insert\_into\_clause](#multiinsertintoclause)::=**

```ebnf+diagram
syntax::= INTO table_reference ["(" (column_name) {"," (column_name)} ")"]
```

**[conditional\_insert\_clause](#conditionalinsertclause)::=**

```ebnf+diagram
syntax::= [(ALL | FIRST)] (WHEN condition THEN (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])})
{" " (WHEN condition THEN (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])})}
[ELSE (multi_insert_into_clause [insert_values_clause]) {" " (multi_insert_into_clause [insert_values_clause])}]
```

### 1. hint

This statement is used to suggest a given plan to the optimizer, prompting it to generate an execution plan for the statement based on this plan. For more details, please refer to [Hint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/hint).

> **Note**: 
>
> The LSC table supports the bulkload hint to accelerate the import of large amounts of data and supports the deduplicate hint for deduplication during insert or BULKLOAD imports.

<span id="singletableinsert" name="singletableinsert" class="yaslink"></span>

### 2. single\_table\_insert

Executed INSERT operation on a single table.

<span id="singleinsertintoclause" name="singleinsertintoclause" class="yaslink"></span>

#### 2.1. single\_insert\_into\_clause

This statement is used to specify the table (including tables in the local database or [remote tables](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink/Syntax Definition of DBLINK)) to insert data, as well as the column fields, and may define an alias for the table.

##### 2.1.1. column\_name

Column field, multiple can be separated by `,`, or all can be omitted, indicating that all columns will be inserted in the order defined by the table's column fields. When specifying column fields, non-null columns without a DEFAULT value must be specified.

All constraints defined on the column fields will also apply to the data being inserted; if constraints are violated, the insertion will fail and an error will be prompted.

***Example***

```sql
-- The DHQ column field of the area table is non-null, an error is prompted
INSERT INTO area VALUES ('01','0000','');
YAS-04006 cannot insert NULL value to column DHQ
  
-- The DHQ column field of the area table is non-null and has a DEFAULT value, inserted successfully
INSERT INTO area (area_no) VALUES ('07');
  
-- The BRANCH_NAME column field of the branches table is non-null and without a DEFAULT value, thus must specify this column for insertion, otherwise an error will be prompted
INSERT INTO branches (branch_no) VALUES ('0202');
YAS-04006 cannot insert NULL value to column BRANCH_NAME
```

<span id="tablereference" name="tablereference" class="yaslink"></span>

##### 2.1.2. table\_reference

This statement is used to specify the object into which data is to be inserted, which can be a table name (including both local database tables or [remote tables](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink/Syntax Definition of DBLINK)), a table partition name, or a view name.

For partitioned tables, if the partition object is not explicitly specified, the system determines which partition table to insert based on the partition item field value. When explicitly specifying:

-  When the partition table type is RANGE, be careful not to exceed the boundary value of the partition for the newly inserted record's partition item field value, otherwise the insertion will fail and an error will be prompted.
- When the partition table type is LIST, ensure that the newly inserted record's partition item field value falls within the value list of its corresponding partition; otherwise, the insertion will fail and an error will be prompted.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- sales_info_range is a range partition table with three existing partitions p_sales_info_range_1 (VALUES LESS THAN('2011')), p_sales_info_range_2 (VALUES LESS THAN('2021')), and p_sales_info_range_3 (VALUES LESS THAN('2031'))
  
-- No partition specified, data inserted successfully based on boundary value
INSERT INTO sales_info_range VALUES ('2012','01','0103','11002',5,100,'');
  
-- Inserting data exceeding the maximum partition value fails
INSERT INTO sales_info_range VALUES ('2040','01','0103','11002',5,100,'');
YAS-02115 partition key does not map to any partition
  
-- '2012' conflicts with boundary value of partition p1, data insert fails
INSERT INTO sales_info_range PARTITION (p_sales_info_range_1) VALUES ('2012','01','0103','11002',5,100,'');
YAS-02120 the partition number is invalid or out-of-range
```

***Example*** for Heap tables

```sql
-- Single base table view
CREATE VIEW view_dml AS SELECT * FROM area WHERE EXISTS (SELECT 1 FROM dual);
INSERT INTO view_dml VALUES('06','SouthwestChina','Chengdu');
SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
06      SouthwestChina                                                          Chengdu


-- Multi base table view: the area_name column in the area table is equi-joined with the primary key column branch_no in the branches table, ensuring that the data from the area table appears at most once in the view, thus the columns belonging to the area table in this view can be manipulated.
CREATE OR replace VIEW view_dml AS SELECT a.area_no, a.area_name, b.branch_no, b.branch_name FROM area a, branches b WHERE area_name = branch_no;

INSERT INTO view_dml(area_no, area_name) VALUES('07','NorthwestChina');
SELECT * FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
07      NorthwestChina                                                          ShenZhen


-- Yet the branches table has no column that corresponds to the primary key or unique index of the area table, and the columns belonging to the branches table in the view view_dml cannot be manipulated. 
INSERT INTO view_dml(branch_no, branch_name) VALUES('07','NorthwestChina');
[1:13]YAS-04942 cannot modify a column which maps to a non key-preserved table
```

###### 2.1.2.1. dblink

This statement indicates that the data to be inserted is a remote table; for detailed descriptions, see [dblink](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink/Syntax Definition of DBLINK).

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

###### 2.1.2.2. partition\_extension\_clause

This statement is used to specify a partition or subpartition.

The partition or subpartition can be specified in two ways:

-  By name
- By providing key values

**PARTITION (partition)**

This statement is used to directly obtain the partition object by name.

***Example*** for Standalone Deployment

```sql
INSERT INTO sales_info_range PARTITION (p_sales_info_range_3) VALUES ('2029','01','0103','11002',5,100,'');
```

**PARTITION FOR (partition_key_value)**

This statement is used to obtain the partition object by providing key values. This compares the key value with the partition boundaries of the table to determine the partition object. The key values can be any value within the boundary. The number of key values must correspond to the number of partition columns, separated by `,`.

***Example*** for Standalone Deployment

```sql
INSERT INTO sales_info_range PARTITION FOR('2015') VALUES ('2012','01','0103','11002',5,100,'');
```

**SUBPARTITION (subpartition)**

This statement is used to directly obtain the subpartition object by name.

In ISC Distributed Cluster Deployment, since the first-level partition is hash-allocated, this method to specify the partition can only be used when the user can determine that the data will fall into a certain partition (for example, by checking corresponding partition key data that already exists in this partition); otherwise, it may lead to data insertion failure.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Obtain the subpartition name of the sales_info table
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
-- The following output assumes a standalone case
PARTITION_NAME               SUBPARTITION_NAME
---------------------------- ---------------------------------------
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_1
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_2
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_3
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_1
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_2
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_3
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_1
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_2
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_3

-- Choose one subpartition to specify for data insertion
INSERT INTO sales_info SUBPARTITION(P_SALES_INFO_1_SP_SALES_INFO_1)
VALUES ('2002','01','0402','11002',5,100,'');
```

**SUBPARTITION FOR (subpartition_key_value)**

This statement is used to obtain the subpartition object according to the provided key values. This compares the key values with the partition boundaries of the table to determine the subpartition object, and the key values can be any value within the boundary range. The number of key values must match the number of partition and subpartition columns, separated by `,`.

***Example***

```sql
INSERT INTO sales_info SUBPARTITION FOR('0103','2018') 
VALUES ('2012','01','0103','11002',5,100,'');
```

#### 2.2. subquery

This statement is used to specify that the data to be inserted is obtained from a subquery. The columns of the insert object must correspond one-to-one with the select_list columns of the subquery in order.

For detailed subquery information, see the subquery portion in the [SELECT](SELECT) documentation.

***Example***

```sql
INSERT INTO sales_info SELECT '2021','01',a.branch_no,'11002',100,1000,'' FROM branches a WHERE area_no ='02';
```

<span id="insertvaluesclause" name="insertvaluesclause" class="yaslink"></span>

#### 2.3. insert\_values\_clause

This statement is used to specify corresponding data values for the columns defined in [single_insert_into_clause](#singleinsertintoclause).

YashanDB supports assigning values to the columns using the following statements:

- [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) expression
	
	- When the result of the expression does not match the data type defined for the column field, the system will first perform a data type conversion, and if the conversion fails, an error will be returned.

	- When the expression is a function, the parameters for the function cannot be subqueries.

*   DEFAULT

	If a DEFAULT value has been defined for the corresponding column, the inserted data will be that DEFAULT value; otherwise, it will be NULL. Based on this rule, if the corresponding column has a non-null constraint, the data insertion will fail.

- For UDT column fields, values are assigned through object initialization methods; see [User UDT](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/User-Defined Types) for descriptions.

The VALUES keyword may be followed by multiple value sets, indicating that a single SQL statement can insert multiple value sets, with a maximum limit of 32768 sets; exceeding this will return the YAS-04816 error.

***Example***

```sql
-- The DHQ field on the area table has already defined a DEFAULT value 'ShenZhen', thus the insertion based on this value is successful
INSERT INTO area VALUES ('08',33005,DEFAULT);
  
-- The BRANCH_NAME field of the branches table is non-null and has no DEFAULT value, so inserting NULL fails
INSERT INTO BRANCHES VALUES ('0202',DEFAULT,'','02');
YAS-04006 cannot insert NULL value to column BRANCH_NAME

-- Two sets of values are inserted into the area_no field, insertion is successful
INSERT INTO area (area_no) VALUES ('20'),('21');

ROLLBACK;
```

<span id="onduplicateclause" name="onduplicateclause" class="yaslink"></span>

#### 2.4. on\_duplicate\_clause

This statement is used to specify that when the inserted data causes unique constraints (including unique indexes) in the table to conflict, instead of reporting an error, the conflicting rows are updated using set_clause.

When there are multiple unique constraints in the table, the system will only handle the operation based on the first constraint item that causes the conflict (determined in the order of declaration of the constraint item).

It should be noted that in ISC Distributed Cluster Deployment, this operation cannot be performed on the primary partition key of a sharded table, otherwise it will return a failure.

<span id="setclause" name="setclause" class="yaslink"></span>

##### 2.4.1. set\_clause

Specifies the fields and values to update, with the rules and requirements for the specified value being consistent with [insert_values_clause](#insertvaluesclause); if the requirements are not met, the update will fail, and the system will return an error.

**column_name_1**

In a standalone HEAP table, the column_name = VALUES(column_name_1) can be used to define the mapping for updating the conflicting row values, with the following constraints:

- column_name and column_name_1 must belong to different columns of the same HEAP table.
- The data type of column_name_1 must support [implicit conversion](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/Data Type Conversion) to the data type of column_name.

***Example***1 for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Add a unique constraint to the DHQ field in the area table, and a primary key constraint already exists on area_no
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           Shanghai       
02      WestChina           Chengdu        
03      SouthChina           Guangzhou      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  

ALTER TABLE area ADD CONSTRAINT c_area_unique UNIQUE (DHQ);

-- When the inserted data causes a conflict with the DHQ unique constraint, modify the conflicting value using set_clause to update the conflicting row
INSERT INTO area VALUES('08',33005,'Shanghai') ON DUPLICATE KEY UPDATE DHQ = 'XiAn';
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           XiAn       
02      WestChina           Chengdu        
03      SouthChina           Guangzhou      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  

-- Both the area_no and DHQ columns in the area table have unique constraints, when both conflict only set_clause affects the first item of the conflict's record
INSERT INTO area VALUES('03',33006,'Chengdu') ON DUPLICATE KEY UPDATE DHQ = 'Shenzhen';
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME        DHQ          
------- ---------------- -------------
01      EastChina           XiAn       
02      WestChina           Chengdu        
03      SouthChina           Shenzhen      
04      NorthChina           Beijing        
05      CentralChina           Wuhan  
```

***Example***2 for ISC Distributed Cluster Deployment

```sql
-- Create area0 table and add a unique constraint to the DHQ field in the area table
CREATE DUPLICATED TABLE area0
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL UNIQUE);
INSERT INTO area0 VALUES ('01','EastChina','Shanghai');
INSERT INTO area0 VALUES ('02','WestChina','Chengdu');
INSERT INTO area0 VALUES ('03','SouthChina','Guangzhou');
INSERT INTO area0 VALUES ('04','NorthChina','Beijing');
INSERT INTO area0 VALUES ('05','CentralChina','Wuhan');

-- When the inserted data causes a conflict with the DHQ unique constraint, modify the conflicting value using set_clause to update the conflicting row
INSERT INTO area0 VALUES('08',33005,'Shanghai') ON DUPLICATE KEY UPDATE DHQ = 'XiAn';

SELECT area_no,area_name,DHQ FROM area0;
AREA_NO AREA_NAME                     DHQ            
------- ----------------------------- ---------------
01      EastChina                        XiAn  
02      WestChina                        Chengdu          
03      SouthChina                        Guangzhou        
04      NorthChina                        Beijing                     
05      CentralChina                        Wuhan           
```

***Example***3 for Standalone Deployment Heap tables

```sql
-- Create area0 table and add a unique constraint to the area_no field in the area table
CREATE TABLE area0
(area_no CHAR(2) NOT NULL UNIQUE,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen');
INSERT INTO area0 VALUES ('01','EastChina','Shanghai');
INSERT INTO area0 VALUES ('02','WestChina','Chengdu');
INSERT INTO area0 VALUES ('03','SouthChina','Guangzhou');
INSERT INTO area0 VALUES ('04','NorthChina','Beijing');
INSERT INTO area0 VALUES ('05','CentralChina','Wuhan');

-- When the inserted data causes a conflict with the DHQ unique constraint, modify the conflicting value using set_clause to update the conflicting row
INSERT INTO area0 VALUES('01','EastChina','XiAn') ON DUPLICATE KEY UPDATE area_no = VALUES(area_no), area_name = VALUES(area_name), DHQ = VALUES(DHQ);

SELECT area_no,area_name,DHQ FROM area0;
AREA_NO AREA_NAME                     DHQ            
------- ----------------------------- ---------------
01      EastChina                        XiAn  
02      WestChina                        Chengdu          
03      SouthChina                        Guangzhou        
04      NorthChina                        Beijing                     
05      CentralChina                        Wuhan           
```

<span id="returningclause" name="returningclause" class="yaslink"></span>

#### 2.5. returning\_clause

This statement indicates that a result set will be returned after data insertion and assigned to specified variables. This statement can only be combined with [insert_values_clause](#insertvaluesclause).

**expr**

Specifies the result columns, and multiple columns can be separated by `,`. Each column must be a [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr); sequences, pseudo-columns, aggregate functions, window functions, and subqueries are not allowed, and binding parameters are not allowed within the expressions.

**variable**

The assignment variables corresponding to expr; depending on the statement used, variable can be a declared variable or a binding parameter. 

This statement can be used in the procedure body or driver client programs, and examples of using this statement as both static and dynamic SQL in the procedure body can be found in the PL manual's [DML Statement](../PL Statements/DML Statement) and [EXECUTE Statement](../PL Statements/EXECUTE Statement).

<span id="multitableinsert" name="multitableinsert" class="yaslink"></span>

### 3. multi\_table\_insert

This statement is applicable to HEAP tables in Standalone Deployment, allowing simultaneous INSERT operations on one or more tables, with multiple tables separated by spaces.

It can specify either value insertion (insert_values_clause) or subquery insertion (subquery).

This statement requires that there be exactly one subquery in the entire statement, and it must be at the end.

<span id="multiinsertintoclause" name="multiinsertintoclause" class="yaslink"></span>

#### 3.1. multi\_insert\_into\_clause

Except that aliases cannot be specified for the tables to be inserted, this statement is consistent with [single_insert_into_clause](#singleinsertintoclause).

For multiple table inserts, the system will allocate a corresponding number of cursors based on the number of tables appearing, with a maximum number of allowable cursors being CURSOR_POOL_SIZE / ((2 * (DB_BLOCK_SIZE + 6000)) + 2920).

#### 3.2. insert\_values\_clause

This statement is used to specify corresponding data values for the columns defined in [multi_insert_into_clause](#multiinsertintoclause).

YashanDB supports assigning values to the columns using the following statements:

-  [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) expression
	
	- When the result of the expression does not match the data type defined for the column field, the system will first perform a data type conversion, and if the conversion fails, an error will be returned.

	- The expression cannot be an UDT construction function, a cast function targeting UDT, or a treat function.

	- When the expression is a function, the parameters for the function cannot be subqueries.

- DEFAULT

	If a DEFAULT value has been defined for the corresponding column, the inserted data will be that DEFAULT value; otherwise, it will be NULL. Based on this rule, if the corresponding column has a non-null constraint, the data insertion will fail.

- For UDT column fields, values are assigned through object initialization methods, as described in [User UDT](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/User-Defined Types).

The VALUES keyword may be followed by multiple sets of values, indicating that a single SQL statement can insert multiple sets of values, with a maximum limit of 32768 sets; exceeding this will return the YAS-04816 error.

<span id="conditionalinsertclause" name="conditionalinsertclause" class="yaslink"></span>

#### 3.3. conditional\_insert\_clause

When performing multiple table conditional inserts, the database filters the conditions after each WHEN clause and only executes the insertion statements for which the condition is true, skipping the insertion statements for which the condition is false.

The constraints for using multiple conditional inserts are as follows:

- A single INSERT statement can contain up to 127 WHEN clauses.

- The WHEN condition clauses, insert_values_clause and subquery cannot use ROWID or ROWSCN.

##### 3.3.1. ALL

The default keyword for conditional insertion is ALL, in which the database executes multiple table inserts sequentially.

##### 3.3.2. FIRST

If the FIRST keyword is specified, the database filters the conditions of each WHEN clause in order and only executes the insertion statement for the first WHEN clause whose condition is true, skipping other WHEN clause insertion statements.

##### 3.3.3. ELSE

If an ELSE clause exists, it will only execute the insertion statements after the ELSE clause if all WHEN clause conditions are false.

#### 3.4. subquery

The subquery can be used to correspondingly specify the data values for the columns defined in [multi_insert_into_clause](#multiinsertintoclause).

- When all tables use insert_values_clause, the SELECT * FROM DUAL statement can be used as a subquery.
- The subquery cannot include UDT construction functions, cast functions targeting UDTs, or treat functions.
- If the subquery returns an empty result set, no data will be inserted into any of the tables.

***Example*** for Standalone Deployment Heap tables

```sql
-- The statement must have a subquery at the end           
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches VALUES('0002','Nanshan','00','----')
           INTO branches VALUES('0003','Futian','00','----')
		   SELECT * FROM DUAL;
		   
-- The subquery must be placed at the last position in the statement
ROLLBACK;
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches 
		   INTO branches VALUES('0003','Futian','00','----')
		   SELECT '0002','Nanshan','00',b.address FROM branches b WHERE b.branch_no='0101';	
           
-- When the subquery returns no results, no data will be inserted
ROLLBACK;
INSERT ALL INTO area VALUES('00','unknown','unknown')
		   INTO branches 
		   INTO branches VALUES('0003','Futian','00','----')
		   SELECT '0002','Nanshan','00',b.address FROM branches b WHERE b.branch_no='0109';
```
