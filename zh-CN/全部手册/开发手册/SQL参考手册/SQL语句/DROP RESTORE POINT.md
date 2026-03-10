通用描述
----

DROP RESTORE POINT用于删除一个已经存在的还原点。

该语句不适用于存算一体分布式集群部署。

执行该语句的用户需拥有FLASHBACK ANY TABLE及以上的权限，如需删除[永久还原点](CREATE RESTORE POINT)必须使用sys用户。

删除还原点前，需确保数据库已开启[全库闪回](ALTER DATABASE.html#flashbackdatabaseclauses)功能，且当前处于MOUNT或OPEN状态。

语句定义
----

**drop restore point::=**

```ebnf+diagram
syntax::= DROP RESTORE POINT restore_point_name
```

### 1. restore\_point\_name

该语句用于指定要删除的还原点的名称，可通过V$RESTORE_POINT视图获取已有还原点信息。

示例（单机、共享集群部署）

```sql
DROP RESTORE POINT p202412011100;
```
