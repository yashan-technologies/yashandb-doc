通用描述
----

DROP MATERIALIZED VIEW用于删除一个物化视图对象。

分布式部署中无物化视图相关功能。

语句定义
----

**drop materialized view::=**

```ebnf+diagram
syntax::= DROP MATERIALIZED VIEW [schema"."] materialized_view_name
```

### 1. materialized\_view\_name

指定要删除的物化视图的名称。

示例（HEAP表）

```sql
DROP MATERIALIZED VIEW mv_refresh;
```

