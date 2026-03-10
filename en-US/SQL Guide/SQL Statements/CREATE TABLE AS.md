## General Description

The CREATE TABLE AS statement creates a new table object based on the results of a subquery, including column fields and their types, while inserting the dataset contained in the query results.

The column fields of the created table have the same names and types as the column items in the subquery, but the column field names can be reset by specifying statements, while the types cannot be changed. The following table properties can also be specified:

*   Column constraints
*   Storage parameters
*   Partition (cannot create partition for temporary tables)
*   Tablespace

Statement Definition
----

**create table as::=**

```ebnf+diagram
syntax::= CREATE [(GLOBAL|PRIVATE) TEMPORARY] TABLE [schema "."] table_name [column_clause] [table_properties] AS subquery
```

**column_clause::=**

```ebnf+diagram
syntax::= "(" (out_of_line_constraint|column_name [DEFAULT default_expr] [inline_constraint])      {"," (out_of_line_constraint|column_name [DEFAULT default_expr] [inline_constraint])} ")"
```

### 1. GLOBAL|PRIVATE TEMPORARY

This statement is used to specify that the created table is a temporary table, with syntax as described in [CREATE TABLE](CREATE TABLE).

### 2. table\_name

This statement is used to specify the name of the table being created, with syntax as described in [CREATE TABLE](CREATE TABLE).

### 3. out\_of\_line\_constraint

This statement is used to define the table's out-of-line constraint items, with syntax as described in [CREATE TABLE](CREATE TABLE).

### 4. column\_name

This statement is used to reset the column field names of the table. If this item is not defined, the table's column fields and types will be created according to all column items and types of the subquery results. If this item is defined, it must correspond to the column items in the subquery one-to-one, and the names must comply with YashanDB's [object naming rules](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

### 5. DEFAULT default\_expr

Defines the default value of the column field, which can be omitted. default_expr can be a literal | expression | function, etc.

### 6. inline\_constraint

Defines inline constraints for the column fields, with syntax as described in [CREATE TABLE](CREATE TABLE).

### 7. table\_properties

This statement is used to specify properties of the table such as storage, partition, tablespace, etc., with syntax as described in [CREATE TABLE](CREATE TABLE).

### 8. subquery

In ISC Distributed Cluster Deployment, subquery statements are not allowed to include distributed system views, otherwise an error is returned.

Syntax as described in [SELECT](SELECT).

***Example***

```sql
--1,  Duplicate all columns and data from the existing table to a new table, including column field names, column field types, and table data, excluding constraint items, storage parameters, partition, and tablespace.
CREATE TABLE branches_copy_all AS SELECT * FROM branches;
-- Adding a false condition can copy only the structure of the duplicated table without copying data.
CREATE TABLE branches_copy_no_data AS SELECT * FROM branches WHERE 1=2;
  
--2,  Duplicate certain columns from the existing table, reset column field names, and define inline constraints. The column fields must correspond one-to-one with the column items of the subquery.
CREATE TABLE branches_copy_column(branch_no1 PRIMARY KEY,branch_name) AS SELECT branch_no,branch_name bname FROM branches;
  
--3,  Duplicate the existing table, and define out-of-line constraints and tablespace.
CREATE TABLE branches_copy_add(FOREIGN KEY (area) REFERENCES area(area_no)) TABLESPACE yashan
AS SELECT branch_no,branch_name,area_no area FROM branches;
```
