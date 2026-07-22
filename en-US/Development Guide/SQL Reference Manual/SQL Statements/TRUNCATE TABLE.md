General Description
----

The TRUNCATE TABLE statement is used to delete all rows from a table (indexes and data in AC will also be deleted), while the space occupied by the table, indexes, and AC will be released and reset to its initial size (unless REUSE STORAGE is specified).

The TRUNCATE operation will delete all data in the table at once, cannot be rolled back, and cannot retrieve previous data through [flashback_query_clause](SELECT).

For parent tables with foreign key constraints defined on child tables, if data already exists in the child table, the parent table cannot be TRUNCATE.

The REUSE STORAGE option listed in this document does not apply to ISC Distributed Cluster Deployment.

Statement Definition
----

**truncate table::=**

```ebnf
= TRUNCATE TABLE [schema "."] table_name [DROP STORAGE|REUSE STORAGE | AS subquery_clause].
```

### DROP STORAGE

This statement specifies that after the table is TRUNCATE, the space it occupies will be released and reset to the table's initial size.

### REUSE STORAGE

This statement specifies that after the table is TRUNCATE, the space it occupies will be retained.

***Example***

```sql
TRUNCATE TABLE finance_info;
```

### AS subquery_clause

This statement is used for the internal syntax of materialized view refresh.

> **Warn**:
>
> **It is not recommended for users to execute manually**, as it may lead to system crashes or other unpredictable results.