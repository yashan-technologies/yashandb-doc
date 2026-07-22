## 通用描述

ALTER PLUGGABLE DATABASE用于修改种子容器或PDB的相关属性。



该语句的适用范围如下：

- 该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），且只能连接根容器执行。

- 该语句不适用于存算一体分布式集群部署。



## 语句定义

**alter pluggable database::=**

```ebnf
= ALTER PLUGGABLE DATABASE ( pdb_name {',' pdb_name}|ALL) (startup_clause|standby_database_clauses).
```

**[startup_clause](#startupclause)::=**

```ebnf
= NOMOUNT | MOUNT | OPEN [RESETLOGS] | CLOSE [NORMAL | IMMEDIATE].
```

**[standby_database_clause](#standbydatabaseclause)::=**

```ebnf
= SWITCHOVER | FAILOVER (RESET ID integer).
```

### pdb_name|ALL

指定待操作的PDB，多个名称间使用逗号（`,`）隔开，指定ALL则表示操作所有PDB。

通过[GV$PDBS](../../../参考手册/系统视图/动态视图/GV$PDBS.md)/[V$PDBS](../../../参考手册/系统视图/动态视图/V$PDBS.md)视图或yasql的`show pdbs`命令可以获取当前所有PDB信息。  

<span id="startupclause" name="startupclause"></span>

### startup_clause

该语句用于启停PDB。

指定ALL启动所有PDB时，不包含种子容器；指定ALL关闭所有PDB时，包含种子容器。指定ALL进行启停操作时，部分PDB操作失败不会阻塞其他PDB的启停，但会提示错误信息。

#### NOMOUNT

启动目标PDB至NOMOUNT阶段。

#### MOUNT

启动目标PDB至MOUNT阶段。

#### OPEN

启动目标PDB至OPEN阶段。

**RESETLOGS**

当数据库进行了PITR（基于时间点的恢复）、数据库闪回或逻辑备库配置时，如果无法进行完全恢复，则需要通过RESETLOGS模式打开数据库，该模式将重新设置redo日志号。

> **Caution**: 
>  
> - 通过指定ALL关键字操作所有PDB时，无法使用RESETLOGS模式打开PDB。
>
> - 若执行完全恢复操作时使用RESETLOGS模式打开PDB，会报YAS-02184错误并关闭实例。

#### CLOSE

关闭目标PDB，关闭选项可省略，默认为NORMAL。

- **NORMAL**：必须等待现有的会话结束后才执行关闭。

- **IMMEDIATE**：尽可能快速的关闭PDB，不会等待现有的会话结束，但是会回滚未提交的事务。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER PLUGGABLE DATABASE pdb1 MOUNT;
 
ALTER PLUGGABLE DATABASE pdb1 OPEN;

ALTER PLUGGABLE DATABASE pdb1 CLOSE;
```

<span id="standbydatabaseclause" name="standbydatabaseclause"></span>

### standby_database_clause

该语句用于执行主备PDB之间的切换。

种子容器无主备，无法且无需执行该语句。

#### SWITCHOVER

该语句用于在主备库同步正常的情况下手动进行主备库的计划内切换（Switchover）。

该语句的使用规则如下：

- 主备库必须均处于OPEN状态。

- 主备databases之间的redo传输链路连接正常，且备库日志同步状态正常。主库查询视图V$ARCHIVE_DEST_STATUS或备库查询视图V$REPLICATION_STATUS可确认相应信息。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER PLUGGABLE DATABASE pdb1 SWITCHOVER;
```

<span id="failover" name="failover"></span>

#### FAILOVER

该语句用于在未开启自动选主且主库异常的情况下手动进行备库的故障切换（Failover），将备库强制切换为主库。

RESET ID仅用于仲裁选主场景yasom自动下发的failover语句中，**不得手动指定该选项**。  

该语句的使用规则如下：

- 当前备库必须处于OPEN状态。

- 当前备库与主库的连接已断开，即备库V$REPLICATION_STATUS视图中CONNECTION=DISCONNECTED。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER PLUGGABLE DATABASE pdb1 FAILOVER;
```
