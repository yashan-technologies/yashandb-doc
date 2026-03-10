General Description
----

DROP MATERIALIZED VIEW is used to delete a materialized view object.

There is no materialized view related functionality in ISC Distributed Cluster Deployment.

Statement Definition
----

**drop materialized view::=**

```ebnf+diagram
syntax::= DROP MATERIALIZED VIEW [schema"."] materialized_view_name
```

### 1. materialized\_view\_name

Specifies the name of the materialized view to be deleted.

***Example*** for Heap tables

```sql
DROP MATERIALIZED VIEW mv_refresh;
```
