General Description
----

CREATE OUTLINE is used to create a stored outline that specifies hint information for a specific SQL statement.

The creation method can be categorized into two types: creating based on SQL_TEXT (the specific SQL statement content) or creating based on SQL_ID (the specific SQL statement's ID value).

When using OUTLINE information, the stored outline created based on SQL_TEXT will be prioritized. The outline created based on SQL_ID will only be used if the SQL_TEXT match is not found. When a SQL statement has OUTLINE information, the optimizer will ignore the hint information in the original statement and directly generate an execution plan based on the OUTLINE, except in exceptional cases. OUTLINE can be used to solidify the execution plan of a specific SQL statement, making it unaffected by external factors such as the runtime environment and statistics fluctuations. Therefore, OUTLINE can serve as a performance tuning tool in specific scenarios. For more detailed information, refer to [SQL Tuning Principles and Rules](../../../Performance Tuning/SQL Tuning/00SQL Tuning).

Users must have the CREATE ANY OUTLINE privilege to create a stored outline.

In a ISC Distributed Cluster Deployment, when executing this statement, the system will check for any abnormal unrecovered DDL statements. If any exist, an error will be reported, and the statement must wait until the corresponding DDL recovery succeeds before it can be executed.

Statement Definition
----

**create\_outline::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [PUBLIC] OUTLINE [outline_name] (([FROM [PUBLIC] source_outline] [FOR CATEGORY category_name] [ON statement]) | ON sql_id USING HINT hint)
```

### 1. OR REPLACE

Replaces an existing OUTLINE.

### 2. PUBLIC

Public schema, default value.

### 3. outline\_name

The name of the OUTLINE to be created, which must not duplicate existing OUTLINE names, cannot include schema names, and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers). 

If omitted, the system will automatically generate an OUTLINE name based on the current time.

### 4. FROM source\_outline

This statement indicates creating a copy from an existing OUTLINE.

For this method of creation, a different category than the source OUTLINE must be specified, and ON statement cannot be used simultaneously.

### 5. FOR CATEGORY category\_name

Specifies the category to which the OUTLINE belongs. If this statement is omitted, the system will default it to the DEFAULT category.

category_name can either be an existing category name or a newly specified category name. category_name cannot include schema names and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

For the same SQL statement, it cannot be specified multiple times in the same category.

### 6. ON statement

Specifies the SQL statement corresponding to the OUTLINE. The SQL statement must be one of the following types and must comply with the respective syntax requirements:

- SELECT statement
- DELETE statement
- UPDATE statement
- INSERT...SELECT statement

For SQL statements that violate syntax requirements, the OUTLINE creation will fail and an error message will be displayed.

### 7. sql\_id

Specifies the SQL statement ID corresponding to the OUTLINE. For detailed information, refer to [V$SQL](../../../Reference Manual/System Views/Dynamic Performance Views/V$SQL).

### 8. hint

Specifies the hint content saved in the OUTLINE, which must conform to YashanDB's [hint specifications](../General SQL Syntax/hint).

***Example***

```sql
-- Create OUTLINE using SQL_TEXT syntax for the given SQL.
CREATE OUTLINE ol_a FOR CATEGORY ctgy_ab ON 
SELECT /*+ FULL(a) */ a.area_name
FROM area a
WHERE a.area_no LIKE '01';

-- Copy from the given source outline and assign it to the default DEFAULT category 
CREATE OUTLINE ol_a1 FROM ol_a;

-- Create OUTLINE using SQL_ID syntax
CREATE OUTLINE ol_s1 ON '6syjr41dya828' USING HINT /*+ FULL(a) */;
```
