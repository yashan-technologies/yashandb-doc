General Description
----

The ALTER MATERIALIZED VIEW statement is used to change the properties related to an existing materialized view.

There is no materialized view related functionality in ISC Distributed Cluster Deployment.

Statement Definition
----

**alter materialized view::=**

```ebnf+diagram
syntax::= ALTER MATERIALIZED VIEW [schema"."] materialized_view_name (alter_mv_refresh_properties |  alter_query_rewrite_clause ) 
```

**alter_mv_refresh_properties::=**

```ebnf+diagram
syntax::= REFRESH ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)]){" " ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)])}
| (NEVER REFRESH)
```

**alter_query_rewrite_clause**

```ebnf+diagram
syntax::= (ENABLE | DISABLE) QUERY REWRITE
```

### 1. materialized\_view\_name

This statement is used to specify the name of an existing materialized view.

### 2. alter\_mv\_refresh\_properties

This statement is used to change the properties related to materialized view refresh, including refresh type, refresh mode, and scheduled refresh, with each item separated by spaces.

#### 2.1. COMPLETE|FORCE

This statement is used to specify the refresh type of the materialized view, including:

- COMPLETE: Full refresh
- FORCE: If the conditions are met, preferentially use fast refresh; if fast refresh is unavailable, use full refresh

At most one refresh type can be specified.

#### 2.2. ON DEMAND|ON COMMIT

This statement is used to specify the refresh mode of the materialized view, including:

- ON DEMAND: Manual refresh, i.e., refresh via the [DBMS_MVIEW](../../PL参考手册/内置高级包/DBMS_MVIEW) advanced package
- ON COMMIT: Automatic refresh, refreshing the associated materialized view at transaction commit

At most one refresh mode can be specified.

#### 2.3. START WITH date|NEXT date

This statement is used to specify the scheduled refresh options for the materialized view, including:

- START WITH date: Specify the time of the first refresh
- NEXT date: Specify the refresh time interval

Scheduled refresh options may specify one or two items.

The next refresh time must be calculated to be a future time.

#### 2.4. NEVER REFRESH

This statement is used to specify that no refresh occurs.

Once specified, it protects the materialized view from any automatic refresh, advanced package refreshing, and ignores any issued refresh statements or advanced package executions.

***Example*** for Heap tables

```sql
ALTER MATERIALIZED VIEW mv_refresh 
REFRESH 
COMPLETE 
ON DEMAND 
NEXT SYSDATE + 1/(24*60);
```

### 3. alter\_query\_rewrite\_clause

This statement is used to change the query rewrite options for the materialized view.

The current state of the materialized view query rewrite functionality can be checked via the `QUERY_REWRITE_ENABLED` parameter.

Query rewrite functionality has the following restrictions:

- Materialized views must not contain mutable built-in functions (such as time-related functions), UDFs, or packages.
- Materialized views must not contain table functions.
- Materialized views must not contain pseudocolumns.
- When the definition of the underlying tables on which the materialized view is based is modified, causing the materialized view to become invalid, query rewrite cannot be performed on that materialized view.
  - Selected base table column data type changes
  - Selected base table column deletion
  - Selected base table column name changes

#### 3.1. (ENABLE|DISABLE) QUERY REWRITE

The specific meanings are as follows:

- ENABLE QUERY REWRITE: Allow the materialized view to be used for query rewriting
- DISABLE QUERY REWRITE: Do not allow the materialized view to be used for query rewriting

***Example*** for Heap tables
```sql
ALTER MATERIALIZED VIEW mv_refresh ENABLE QUERY REWRITE;
```
