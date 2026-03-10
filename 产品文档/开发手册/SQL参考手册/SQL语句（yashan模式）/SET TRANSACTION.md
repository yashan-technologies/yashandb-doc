通用描述
----

SET TRANSACTION用于设置事务的隔离级别，事务的隔离级别有：

*   读已提交（READ COMMITTED）：系统默认的隔离级别，只能读取已提交的数据版本，事务每条语句支持语句级一致性读。
*   读当前提交（CURRENT COMMITTED）：是读已提交的一种，只能读取已提交的数据版本，但无法保证语句内的读一致性。
*   可串行化（SERIALIZABLE）：最严格的隔离级别，提供事务级一致性读、完整的写写串行化冲突检测机制。

分布式部署中的事务隔离级别默认为READ COMMITTED，用户无法执行本语句。

语句定义
----

**set transaction::=**

```ebnf+diagram
syntax::= SET TRANSACTION [ISOLATION LEVEL (READ COMMITTED|CURRENT COMMITTED|SERIALIZABLE)] [NAME comment_string]
```

### 1. READ COMMITTED

将事务隔离级别设置为读已提交。

示例（单机、共享集群部署）

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 2. CURRENT COMMITTED

将事务隔离级别设置当前提交。

示例（单机、共享集群部署）

```sql
SET TRANSACTION ISOLATION LEVEL CURRENT COMMITTED;
```

### 3. SERIALIZABLE

将事务隔离级别设置为串行化。

示例（单机、共享集群部署）

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```
