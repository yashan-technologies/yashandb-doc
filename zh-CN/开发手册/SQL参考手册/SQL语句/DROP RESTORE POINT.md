通用描述
----

DROP RESTORE POINT用于删除一个已经存在的还原点。

该语句不适用于存算一体分布式集群部署。

当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，根容器与各个PDB上的还原点相互独立，需连接目标容器执行还原点相关的管理操作，且只对自身生效。


执行该语句的用户需拥有FLASHBACK ANY TABLE及以上的权限，如需删除[永久还原点](CREATE RESTORE POINT.md)必须使用sys用户。

删除还原点前，需确保数据库已开启[全库闪回](ALTER DATABASE.md#flashbackdatabaseclauses)功能，且当前处于MOUNT或OPEN状态。

语句定义
----

**drop restore point::=**

```ebnf
= DROP RESTORE POINT restore_point_name.
```

### restore\_point\_name

该语句用于指定要删除的还原点的名称，可通过V$RESTORE_POINT视图获取已有还原点信息。

示例（单机/共享集群/分布式集群部署）

```sql
DROP RESTORE POINT p202412011100;
```
