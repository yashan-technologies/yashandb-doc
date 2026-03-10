通用描述
----

COMMIT用于提交一个事务。

单机部署中，COMMIT等同于COMMIT WORK WRITE WAIT IMMEDIATE。

分布式部署中，COMMIT等同于COMMIT WORK WRITE WAIT。

执行COMMIT操作前，用户在事务过程做的任何修改只有自己能看到，其他用户无法看到，并可以通过回滚（Rollback）恢复修改之前的数据。

执行COMMIT操作时，系统对用户所做的修改进行数据持久化（redo日志写入磁盘，触发磁盘I/O），同时清除事务所在会话所有的SAVEPOINT，释放本事务持有的所有锁，本次事务结束。

执行COMMIT操作后，所有用户都能看到修改后的数据，且不能通过回滚恢复修改之前的数据。

系统在如下时间点自动调用COMMIT语句：

*   DDL操作开始前。
*   DDL操作成功结束后。
*   会话正常退出登录时。

语句定义
----

**commit::=**
```ebnf+diagram
syntax::= COMMIT [WORK] (([WRITE [IMMEDIATE|BATCH] [WAIT|NOWAIT]]) | FORCE GTID["," SCN])
```

### 1. WORK

该语句用于和标准SQL的语法兼容，无实际意义。

### 2. FORCE

该语句用于在分布式事务中，对指定的事务强制提交。

GTID：从[GV$2PC_PENDING视图](../../../参考手册/系统视图/动态视图/GV$2PC_PENDING)中获得。该参数对FORCE语句不可省略。

SCN：为本次提交分配一个SCN号，该参数可省略，则使用当前SCN号。

示例（分布式部署）
```sql
-- 此GTID仅为示例，实际应从GV$2PC_PENDING视图中获得。
COMMIT FORCE 1234567;

-- 此SCN仅为示例，实际应比GV$2PC_PENDING视图中对应事务的SCN大。
COMMIT FORCE 1234567,12345;
```

### 3. WRITE

该语句用于指定redo日志写入磁盘的方式，默认为WRITE WAIT IMMEDIATE。

在分布式部署中，不论是否使用WRITE子句或采用WRITE子句的任意组合，其效果全等同于COMMIT WORK WRITE WAIT IMMEDIATE。

#### 3.1. IMMEDIATE|BATCH

指定redo日志何时写入磁盘，IMMEDIATE表示立即写入磁盘，BATCH表示将系统中需要执行WRITE的redo数据累积到一定数量后再一起写入磁盘。

通过V$REDOSTAT视图的BATCH_COMMIT_DELAY字段可查看当前系统在BATCH模式下redo数据累积刷盘所延迟的时间。  

#### 3.2. WAIT|NOWAIT

指定是否进行写操作等待，WAIT表示直到redo日志成功写入磁盘后，COMMIT才会进行成功返回，NOWAIT表示COMMIT直接进行成功返回，此时不保证redo日志成功写入磁盘。

> **Note**：
>
> 当配置参数COMMIT_WAIT被设置为WAIT_FORCE时，即使指定了NOWAIT，系统仍强制执行等待。

示例

```sql
COMMIT WRITE IMMEDIATE NOWAIT;
```
