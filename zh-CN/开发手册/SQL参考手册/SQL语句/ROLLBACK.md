通用描述
----

ROLLBACK用于回滚一个事务。以下为不指定SAVEPOINT时的回滚规则：

*   执行ROLLBACK操作前，用户可以实时看到自己修改后的数据，其他用户无法看到。
*   执行ROLLBACK操作时，系统将Data Buffer里的数据恢复为修改之前的值，同时清除事务所在会话所有的SAVEPOINT，释放本事务级别的所有锁，本次事务结束。
*   执行ROLLBACK操作后，所有用户都只能看到修改前的数据。

在YashanDB中，系统发生任何异常情况时，均会自动调用ROLLBACK。

语句定义
----

**rollback::=**

```ebnf
= ROLLBACK [WORK] (([TO [SAVEPOINT] savepoint_name]) | FORCE GTID).
```

### WORK

该语句用于和标准SQL的语法兼容，无实际意义。

### FORCE

该语句用于在分布式事务中，对指定的事务强制回滚。

GTID：从[GV$2PC_PENDING视图](../../../参考手册/系统视图/动态视图/GV$2PC_PENDING)中获取，该参数对FORCE语句不可省略。

示例（存算一体分布式集群部署）

```sql
-- 示例语句提供的GTID仅为示例，实际应从GV$2PC_PENDING视图中获取。
ROLLBACK FORCE 1234567;
```

### TO SAVEPOINT

该语句用于回滚指定保存点之后的操作，执行此语句时：

*   系统将Data Buffer里的数据恢复为该保存点时的数值。
*   清除该保存点之后的所有的SAVEPOINT。
*   不结束事务。

示例

```sql
-- 回滚整个事务
ROLLBACK;

-- 回滚到指定保存点
ROLLBACK TO SAVEPOINT sa_area_1;
```
