通用描述
----

TRUNCATE TABLE语句用于删除表的所有数据行（索引，AC里的数据也将被删除），同时该表及索引，AC所占的数据空间将被释放并被重置到其初始大小（除非指定了REUSE STORAGE）。

TRUNCATE操作将会一次性删除表的所有数据，不能回滚（Rollback），也不能通过[flashback\_query\_clause](./SELECT)获得操作之前的数据。

对于被子表定义了外键约束的父表，若子表中已存在数据，则无法TRUNCATE此父表。

在共享集群/分布式集群部署中，只能在主实例（GV$INSTANCE视图中INSTANCE_ROLE=MASTER的实例）上对LSC表执行TRUNCATE TABLE操作。

本文所列REUSE STORAGE选项不适用于存算一体分布式集群部署。

语句定义
----

**truncate table::=**

```ebnf
= TRUNCATE TABLE [schema "."] table_name [DROP STORAGE|REUSE STORAGE | AS subquery_clause].
```

### DROP STORAGE

该语句用于指定表被TRUNCATE后，其所占数据空间将被释放并被重置到该表的初始大小值。

### REUSE STORAGE

该语句用于指定表被TRUNCATE后，其所占数据空间仍保留。

示例

```sql
TRUNCATE TABLE finance_info;
```

### AS subquery_clause

该语句为物化视图刷新使用的内部语法。

> **Warn**:
>
> **不建议用户手动执行**，若不避免可能会导致系统崩溃或其他不可预知的结果。
