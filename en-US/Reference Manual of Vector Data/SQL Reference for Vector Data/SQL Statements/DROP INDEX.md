## General Description

DROP INDEX is used to delete an index object, and at this time, all the data space occupied by the index is also released.

## Statement Definition

**drop index::=**

```ebnf
= DROP INDEX [schema"."] index_name.
```

***Example*** for Heap tables

```sql
DROP INDEX HNSW_INDEX;
```
