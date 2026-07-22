## 通用描述

DROP INDEX用于删除一个索引对象，此时该索引所占的数据空间也全部被释放。

## 语句定义

**drop index::=**

```ebnf
= DROP INDEX index_name ON [schema "."] table_name.
```

**alter table drop index::=**

```ebnf
= ALTER TABLE [schema "."] table_name DROP INDEX index_name.
```

示例（单机HEAP表）

```sql
DROP INDEX HNSW_INDEX ON VECTOR_TABLE;
```
