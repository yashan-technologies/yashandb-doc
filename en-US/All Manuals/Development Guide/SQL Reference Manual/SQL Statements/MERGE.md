General Description
----

The MERGE statement integrates multiple steps of SELECT/UPDATE/INSERT/DELETE operations. It allows you to consolidate records from the source (Source) object to the target (Target) object based on a condition (Condition) in a single statement.

This statement is applicable to standalone HEAP/TAC tables and YAC Deployment.

At least one of merge_update_clause and merge_insert_clause must be defined in the statement.

The MERGE statement consolidates operations of SELECT/UPDATE/INSERT/DELETE and is subject to the same conditions as those found in the SELECT/UPDATE/INSERT/DELETE statements that appear within it.

MERGE is a deterministic statement; you cannot update the same row of the target table multiple times within the same MERGE statement.

Statement Definition
----

**merge::=**

```ebnf+diagram
syntax::= MERGE [hint] INTO target_table_clause USING source_table_clause ON "(" condition ")" (merge_update_clause | merge_insert_clause) {" " (merge_update_clause | merge_insert_clause)}
```

**[target\_table\_clause](#targettableclause)::=**

```ebnf+diagram
syntax::= ([schema "."] (table_name [partition_extension_clause] | view_name) | ( "(" (subquery) [subquery_restriction_clause] ")")) [t_alias]
```

**See subquery in [SELECT](SELECT)**

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= partition (("(" partition ")")|(for "(" (partition_key_value) {"," (partition_key_value)} ")"))
```

**[subquery\_restriction\_clause](#subqueryrestrictionclause)::=**

```ebnf+diagram
syntax::= ((WITH) ((READ ONLY) | (CHECK OPTIION)) [CONSTRAINT constraint])
```

**[source\_table\_clause](#sourcetableclause)::=**

```ebnf+diagram
syntax::= ([schema "." ] ((table [partition_extension_clause])|view)|"(" subquery ")") [t_alias]
```

**See subquery in [SELECT](SELECT)**

**[merge\_update\_clause](#mergeupdateclause)::=**

```ebnf+diagram
syntax::= WHEN MATCHED THEN UPDATE SET (column "=" (expr|DEFAULT)) {"," (column "=" (expr|DEFAULT))} [where_clause] [DELETE where_clause]
```

**[merge\_insert\_clause](#mergeinsertclause)::=**

```ebnf+diagram
syntax::= WHEN NOT MATCHED THEN INSERT ["(" (column) {"," (column)}")"] VALUES "(" (expr|DEFAULT) {"," (expr|DEFAULT)} ")" [where_clause]
```

### 1. hint

This clause is used to provide a specific strategy to the optimizer, allowing it to generate an execution plan based on this strategy. [See hint description](../General SQL Syntax/hint).

<span id="targettableclause" name="targettableclause" class="yaslink"></span>

### 2. target\_table\_clause

This clause is used to specify the target for the MERGE operation, which can be the name of a local table, a table partition name, a view name, or a subquery. An alias can be specified for it.

When operating on a base table via a view or subquery, the following constraints apply:

- The subquery or the subquery of the target view cannot be a multi-base table join.
- You cannot use operators or operations such as grouping, aggregation, deduplication, ROWNUM, CONNECT BY, sampling, set operations, window functions, for update, table functions, etc., on the base table.
- The columns operated on must correspond to the columns of the base table (other expression types are not allowed).

#### 2.1. table\_name

The name of the target table.

#### 2.2. view\_name

The name of the target view.

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

#### 2.3. partition\_extension\_clause

Consistent with the description in the [INSERT](INSERT) statement.

<span id="subqueryrestrictionclause" name="subqueryrestrictionclause" class="yaslink"></span>

#### 2.4. subquery\_restriction\_clause

Consistent with the description of subquery_restriction_clause in the [CREATE VIEW](CREATE VIEW) statement.

It can be omitted.

#### 2.5. t\_alias

Defines an alias.

<span id="sourcetableclause" name="sourcetableclause" class="yaslink"></span>

### 3. source\_table\_clause

This clause is used to specify the source for the MERGE operation, which can be a table, table partition, view, or result set from a subquery.

#### 3.1. view

The name of the view.

#### 3.2. subquery

Consistent with the description in the [SELECT](SELECT) statement.

<span id="condition" name="condition" class="yaslink"></span>

### 4. condition

This clause specifies the general [condition statement](../General SQL Syntax/condition) for YashanDB, which defines the condition to select the next operation for the MERGE. Each row in the target table will be evaluated against this condition. If true (MATCHED), the merge_update_clause will be executed; if false (NOT MATCHED), the merge_insert_clause will be executed.

The condition can be any expression that produces a Boolean result, for example:

*   TRUE, which results in a Boolean value of TRUE.
*   1=2, which results in a Boolean value of FALSE.
*   target.column=source.column, which evaluates to TRUE if a matching row exists in the Source for the Target row by the column field; otherwise it evaluates to FALSE.

<span id="mergeupdateclause" name="mergeupdateclause" class="yaslink"></span>

#### 4.1. merge\_update\_clause

This clause is used to execute an UPDATE statement on specified columns of the target table when the [condition](#condition) evaluates to true. Multiple columns can be specified, separated by a `,`. 

If an UPDATE clause is present, the `UPDATING ('*column*')` condition predicate triggered by MERGE will return TRUE when the updated columns match.

The values assigned to column fields can be:

*   [expr](../General SQL Syntax/expr) expressions
*   DEFAULT: used in the same way as in the [UPDATE](UPDATE) statement.

#### 4.2. where\_clause

For the rows in the target table that enter the UPDATE statement based on the ON condition above, a condition evaluation (grammar consistent with [condition](#condition)) will be executed. Only the rows that satisfy this condition will have their data updated.

#### 4.3. DELETE where\_clause

For the rows in the target table that have been updated, another condition check (grammar consistent with [condition](#condition)) will be executed. Rows satisfying this condition will be deleted from the target table.

The DELETE operation will execute DELETE directly rather than performing an UPDATE followed by a DELETE, which will affect the behavior of UPDATE/DELETE event triggers.

***Example*** for Standalone Deployment Heap tables and TAC tables and YAC Deployment

```sql
-- employees is a table containing employee information with the following five rows
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-08                   
0101   000        0101000002    John          1     2017-12-13                  
0201   010        0201010011    Anna          0     2022-08-09                  
0201   008        0201008003    Jack          1     2021-07-05                  
0101   008        0201008004    Jim           1     2022-11-17 
  
-- Create employees_merge table with the same structure as employees
CREATE TABLE employees_merge AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees_merge VALUES ('0101','008','0201008003','Jim','0',DATE '2021-11-17');
INSERT INTO employees_merge VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
  
-- Merge data from employees_merge table
  
-- Since employee number 0201008003 exists in two rows in employees, utilize conditions of department and branch to filter it into one row, otherwise the update will fail
MERGE INTO employees_merge b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHERE a.department='008' AND a.branch='0201';
  
-- After merging, data in employees_merge is updated as follows
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees_merge;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   008        0201008003    Jim           1     2021-07-05                  
0101   000        0101000002    John          1     2021-11-17
  
-- Adding a DELETE statement and re-merging; Jim's data is updated then deleted
MERGE INTO employees_merge b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHERE a.department='008' AND a.branch='0201'
DELETE WHERE b.department='008';
  
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees_merge;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000002    John          1     2021-11-17
```

<span id="mergeinsertclause" name="mergeinsertclause" class="yaslink"></span>

### 5. merge\_insert\_clause

This clause is used to execute an INSERT statement on the target table when the [condition](#condition) evaluates to false. Failing to specify column fields means values will be matched according to the order of defined columns in the target table; multiple columns can be specified, separated by a `,`.

The values assigned to column fields can be:

*   [expr](../General SQL Syntax/expr) expressions
*   DEFAULT: used in the same way as in the [INSERT](INSERT) statement.

#### 5.1. where\_clause

For the rows in the target table that are entering the INSERT statement based on the above ON condition, a condition evaluation (grammar consistent with [condition](#condition)) will be executed. Only the rows that satisfy this condition will be inserted into the target table.

***Example*** for Standalone Deployment Heap tables and TAC tables and YAC Deployment

```sql
-- employees is a table containing employee information with the following five rows
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-08                   
0101   000        0101000002    John          1     2017-12-13                   
0201   010        0201010011    Anna          0     2022-08-09                   
0201   008        0201008003    Jack          1     2021-07-05                  
0101   008        0201008004    Jim           1     2022-11-17
  
-- Create employees3 table with the same structure as employees
CREATE TABLE employees3 AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees3 VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
  
-- Merge data from employees1 table
MERGE INTO employees3 b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHEN NOT MATCHED THEN INSERT VALUES (a.branch,a.department,a.employee_no,a.employee_name,a.sex,a.entry_date);
  
-- Records of employee John in employees3 are updated, and records not found in employees are inserted
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees3;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000002    John          1     2017-12-13                 
0101   000        0101000001    Mask          1     2020-09-08                  
0201   010        0201010011    Anna          0     2022-08-09                   
0201   008        0201008003    Jack          1     2021-07-05                   
0101   008        0201008004    Jim           1     2022-11-17   
```
