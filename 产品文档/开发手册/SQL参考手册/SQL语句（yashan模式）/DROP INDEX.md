通用描述
----

DROP INDEX用于删除一个索引对象，此时该索引所占的数据空间也全部被释放。

当分区索引被删除时，其所有的索引分区也被删除。

语句定义
----

**drop index::=**

```ebnf+diagram
syntax::= DROP INDEX [schema"."] index_name
```

### 1. index\_name

指定要删除的索引的名称。

示例（HEAP表、TAC表）

```sql
DROP INDEX idx_sales_info_1;
```

