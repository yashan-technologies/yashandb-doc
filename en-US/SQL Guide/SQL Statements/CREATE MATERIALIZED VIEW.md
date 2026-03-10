General Description
----

The CREATE MATERIALIZED VIEW statement is used to create a materialized view that contains the results of a subquery. Unlike a normal view, a materialized view occupies physical storage space.

A materialized view can only be created based on HEAP tables in Standalone Deployment or YAC Deployment; there is no materialized view related functionality in ISC Distributed Cluster Deployment.

Creating a materialized view requires the following privileges:

-  Materialized view creator (the user executing the CREATE MATERIALIZED VIEW statement): should have the privilege to create materialized views.
- Materialized view owner (the user corresponding to the schema of the materialized view): should have the privilege to create tables and access (SELECT or READ) the base tables of the materialized view.
- When the creator and owner are the same user, they must possess all the above privileges simultaneously.

Statement Definition
----

**create materialized view::=**

```ebnf+diagram
syntax::= CREATE MATERIALIZED VIEW [SCHEMA "."] materialized_view_name 
["("((column_name) {"," (column_name)})")"]
[TABLESPACE tablespace_name] 
[BUILD (IMMEDIATE | DEFERRED)] 
[create_mv_refresh_clause] 
[query_rewrite_clause] 
AS subquery
```

**create_mv_refresh_clause::=**

```ebnf+diagram
syntax::= REFRESH ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)]){" "([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)])}
| (NEVER REFRESH)
```

**query_rewrite_clause**

```ebnf+diagram
syntax::= (ENABLE | DISABLE) QUERY REWRITE
```

### 1. materialized\_view\_name

This statement is used to specify the name of the materialized view to be created; it cannot be omitted and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

If `SCHEMA.materialized_view_name` is used, the current user must have the privilege to create materialized views in the specified schema, and the target schema user must have the privilege to create tables.

#### 1.1. column\_name

This statement is used to specify aliases for the columns of the materialized view; it can be omitted, does not specify data types, and must comply with YashanDB's [object naming conventions](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

### 2. TABLESPACE

This statement is used to specify the tablespace in which the persistent data of the created materialized view is stored.

***Example*** for Heap tables

```sql
CREATE MATERIALIZED VIEW mv1 TABLESPACE USERS AS SELECT * FROM area;
```

### 3. BUILD (IMMEDIATE|DEFERRED)

This statement is used to specify whether the materialized view synchronously refreshes data during creation; it can be omitted, in which case the default is BUILD IMMEDIATE.

- IMMEDIATE indicates that the data is refreshed when the materialized view is created; this option will make the creation process take longer.

- DEFERRED indicates that only the basic definition of the materialized view is created without refreshing data, and subsequent refreshes are conducted by other means.

***Example*** for Heap tables

```sql
CREATE MATERIALIZED VIEW mv2 BUILD IMMEDIATE AS SELECT * FROM area;
```

### 4. create\_mv\_refresh\_clause

This statement is used to specify properties related to the refresh of the materialized view, including refresh type, refresh mode, scheduled refresh, etc., with multiple items separated by spaces.

#### 4.1. COMPLETE|FORCE

This statement is used to specify the refresh type of the materialized view. You can specify either COMPLETE or FORCE, or omit it, and regardless of the specified value, a full refresh will take effect.

#### 4.2. ON DEMAND|ON COMMIT

This statement is used to specify the refresh mode of the materialized view, including:

- ON DEMAND: manual refresh, i.e., using the [DBMS_MVIEW](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_MVIEW) advanced package for refresh
- ON COMMIT: automatic refresh, which refreshes related materialized views at the time of transaction commit

At most one refresh mode can be specified; if omitted, the default is ON DEMAND.

#### 4.3. START WITH date|NEXT date

This statement is used to specify the scheduled refresh options of the materialized view, including:

- START WITH date: specifies the time of the first refresh
- NEXT date: specifies the refresh time interval

Scheduled refresh options can be specified as one or both; omitting them indicates no scheduled refresh.

The next refresh time must be a future time calculated thereafter.

#### 4.4. NEVER REFRESH

This statement is used to specify no refresh.

This will protect the materialized view from any automatic refresh, advanced package refresh, and will ignore any issued refresh statements or advanced package execution.

***Example*** for Heap tables
```sql
CREATE MATERIALIZED VIEW mv_refresh(mya,myb) BUILD IMMEDIATE
REFRESH
COMPLETE
ON DEMAND
NEXT SYSDATE + 10/(2460)
AS SELECT area_no,area_name FROM area;
```

### 5. query\_rewrite\_clause

This statement is used to specify whether this materialized view can be used for query rewrite functionality; it can be omitted, in which case it is defaulted to not being usable for query rewrite.

The current functionality of materialized view query rewrite can be viewed through the `QUERY_REWRITE_ENABLED` parameter.

The query rewrite functionality includes the following restrictions:

- Materialized views are not allowed to contain variable built-in functions (such as time-related functions), UDFs, and packages.
- Materialized views are not allowed to contain table functions.
- Materialized views are not allowed to contain pseudo-columns.
- When the definition of the table on which the materialized view is based is modified and causes the materialized view to become invalid, the materialized view cannot be used for query rewrite. Modifications to the table definition include:
  - Changes to the data types of selected base table columns
  - Deletion of selected base table columns
  - Changes to the names of selected base table columns

#### 5.1. (ENABLE|DISABLE) QUERY REWRITE

The specific meanings are as follows:

- ENABLE QUERY REWRITE: allows the materialized view to be used for query rewrite.

- DISABLE QUERY REWRITE: does not allow the materialized view to be used for query rewrite.

### 6. subquery

This statement is used to specify the subquery statement for creating the materialized view; the rules for subqueries are described in the [SELECT](SELECT) statement. The columns in the subquery cannot be specified as a sequence number or UDT column.

If column fields for the materialized view are specified during creation, the number of column fields in the subquery must match the number of column fields in the materialized view; otherwise, an error will be returned.

If a `SELECT *` operation is included on a table in the subquery:

* The created materialized view will include all column fields of that table as its own column fields.
* If the table adds new column fields, this materialized view will not reflect those changes and will still only include the original column fields.
* If the table deletes a column field, this materialized view will become invalid and cannot be used until that column field is added back.
* If the table renames a column field, this materialized view will become invalid and cannot be used until that column field is renamed back to its original name.

***Example*** for Heap tables

```sql
CREATE MATERIALIZED VIEW mv_subqu AS SELECT * FROM area;

CREATE MATERIALIZED VIEW mv_subquery(mya,myb) AS SELECT b.branch_name, a.area_name
FROM branches b, area a
WHERE a.area_no=b.area_no AND b.branch_no LIKE '01%';

-- An error will be returned when the number of column fields does not match
CREATE MATERIALIZED VIEW mv_colmismatch(c1,c2,c3) AS SELECT area_no,area_name FROM area;
[1:26]YAS-04304 the count of column is mismatched
```
