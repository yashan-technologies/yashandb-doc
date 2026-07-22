General Description
----

DROP INDEX is used to delete an index object, at which point all the data space occupied by the index is also released.

When a partition index is deleted, all its index partitions are also deleted.

Statement Definition
----

**drop index::=**

```ebnf
= DROP INDEX [schema"."] index_name.
```

### index\_name

Specifies the name of the index to be deleted.

***Example*** for Heap tables and TAC tables

```sql
DROP INDEX idx_sales_info_1;
```
