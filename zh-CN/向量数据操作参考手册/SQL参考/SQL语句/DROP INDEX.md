## 通用描述

DROP INDEX用于删除一个索引对象，此时该索引所占的数据空间也全部被释放。

## 语句定义

**drop index::=**

```ebnf
= DROP INDEX [schema"."] index_name.
```

示例（HEAP表）

```sql
DROP INDEX HNSW_INDEX;
```
