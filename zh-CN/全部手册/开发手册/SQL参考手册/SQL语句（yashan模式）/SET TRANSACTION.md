通用描述
----

SET TRANSACTION用于设置事务的隔离级别，事务的隔离级别有：

*   读已提交（READ COMMITTED）：系统默认的隔离级别，只能读取已提交的数据版本，事务每条语句支持语句级一致性读。
*   可串行化（SERIALIZABLE）：最严格的隔离级别，提供事务级一致性读、完整的写写串行化冲突检测机制。

存算一体分布式集群部署中的事务隔离级别默认为READ COMMITTED，用户无法执行本语句。

对dblink远端数据库进行事务操作时，只支持READ COMMITTED事务隔离级别。

语句定义
----

**set transaction::=**

```ebnf+diagram
syntax::= SET TRANSACTION [ISOLATION LEVEL (READ COMMITTED|SERIALIZABLE)] [NAME comment_string]
```

### 1. READ COMMITTED

将事务隔离级别设置为读已提交。

示例（单机、共享集群部署）

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 2. SERIALIZABLE

将事务隔离级别设置为串行化。

示例（单机、共享集群部署）

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```
