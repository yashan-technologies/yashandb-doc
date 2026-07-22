## General Description

DROP INDEX is used to delete an index object, and at this time, all the data space occupied by the index is also released.

## Statement Definition

**drop index::=**

```ebnf
= DROP INDEX index_name ON [schema "."] table_name.
```

**alter table drop index::=**

```ebnf
= ALTER TABLE [schema "."] table_name DROP INDEX index_name.
```

***Example*** for Standalone Deployment Heap tables

```sql
DROP INDEX HNSW_INDEX ON VECTOR_TABLE;
```
