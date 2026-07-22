本文主要介绍PDB级别的启停操作，且所有操作的前提是根容器已处于OPEN状态。关于CDB级别的启停则请参考相应指导：

- 单机部署：CDB启停等同于[实例启停](../../实例管理/实例启停.md)。

- 共享集群/分布式集群部署：CDB启停等同于[集群启停](../../集群管理/集群启停.md)。

## 启停流程及运行模式介绍

### PDB启动三阶段

PDB从关闭启动至正常使用需要经过以下阶段：

*   **NOMOUNT**：启动PDB，此时读取参数文件，但不加载数据库；本阶段仅允许sys用户登录。

*   **MOUNT**：启动PDB，读取控制文件，加载数据库，但数据库处于关闭状态；本阶段仅允许sys用户登录。共享集群/分布式集群部署中的PDB无此阶段。

*   **OPEN**：启动PDB，加载并打开数据库。打开数据库时还可以按需选择[数据库运行模式](#open_mode)为READWRITE、READONLY或RESETLOGS。

<span id="open_mode" name="open_mode"></span>

### 数据库运行模式

PDB启动至OPEN阶段时，支持READWRITE、READONLY、RESETLOGS和UPGRADE四种打开模式。

- **READWRITE**：数据库默认打开为READWRITE模式，该模式下数据库支持完整的事务读写操作，用于正式生产环境。

- **READONLY**：以只读模式打开数据库，限制数据库只读，不产生任何redo。种子容器默认且只能使用READONLY模式打开。主备部署时，物理备库默认使用READONLY模式打开。

- **UPGRADE**：数据库升级时，升级工具yasboot使用此模式打开数据库，该模式下不允许建立新的会话连接，也不允许以该模式手动OPEN数据库。

- **RESETLOGS**：当数据库进行了PITR（基于时间点的恢复）、数据库闪回或逻辑备库配置时，如果无法进行完全恢复，则需要通过RESETLOGS模式打开数据库，该模式将重新设置redo日志号。



### 关库模式

YashanDB支持按以下3种模式关闭PDB：

*   NORMAL：等待事务正常结束后关闭PDB，没有等待时间限制，建议选择该模式关闭PDB。

*   IMMEDIATE：强制中断所有数据库操作，将未完成的事务回退，等待脏页刷盘后关闭PDB。

*   ABORT：强制中断所有数据库操作并关闭PDB，不等待脏页刷盘。这种关闭模式不等待脏页刷盘，会使启动时间变长。

  > **Caution**: 
  >
  > 仅当服务器宕机、断电或人为强制关库时才建议使用ABORT模式，否则应避免使用该模式。



<span id="Load" name="Load"></span>

## 共享/分布式集群部署中PDB实例负载


在共享集群/分布式集群部署中，每个PDB可以运行多个实例（即可在每台服务器上启动0 - 1个实例）并发读写同一份数据。在实际使用中，可基于服务器资源状况、部分PDB高可用需求等因素，综合考虑均衡负载每个PDB的实例分布。


- 在启动PDB时，使用ycsctl start pdb命令的可选参数-node可以按需指定PDB启动后运行于指定的部分/全部服务器上。

- 在负载高峰期，可选择性地临时关闭（使用ycsctl stop pdb命令）较高负载服务器上的部分PDB实例，例如让某些业务低峰PDB仅运行1个实例。

## 启动PDB

启动PDB时，其启动阶段可以按NOMOUNT -> MOUNT -> OPEN依次或跳级指定，但无法回退。

启动PDB可以使用以下方法：

| 方法 | 功能说明 |
| -------------------------------- | -------------------------------------------- |
| [yasboot工具](../../../工具手册/yasboot/yasboot命令介绍/yasboot pdb.md) | 可以将PDB启动至任意阶段，且可以执行批量PDB启动，但无法指定PDB的运行模式。<br/>在共享集群/分布式集群部署的容器数据库，只能将PDB从关闭状态启动至任意阶段，无法从过程态（NOMOUNT、MOUNT）启动至下一阶段或OPEN。 |
| 连接根容器执行[ALTER PLUGGABLE DATABASE](../../../开发手册/SQL参考手册/SQL语句/ALTER PLUGGABLE DATABASE.md)语句 | 可以将指定PDB启动至任意阶段，且可以执行批量PDB启动，但无法指定PDB的运行模式。 |
| 直连目标PDB执行[ALTER DATABASE](../../../开发手册/SQL参考手册/SQL语句/ALTER DATABASE.md)语句 | 可以将PDB从NOMOUNT阶段调整到MOUNT阶段或OPEN阶段，但ALTER DATABASE语句仅对执行PDB生效。 |
| [ycsctl工具](../../../工具手册/ycsctl/ycsctl使用指导/数据库资源管理命令.md)                                               | 仅适用于共享集群/分布式集群部署的容器数据库。<br />可以将指定PDB从关闭状态启动至任意阶段，但无法指定PDB的运行模式、无法从过程态（NOMOUNT、MOUNT）启动至下一阶段或OPEN。 |

<span id="nomount" name="nomount"></span>

### 启动至NOMOUNT阶段

::: tabs
== 使用yasboot工具

以数据库安装用户登录数据库所在服务器，使用yasboot工具将PDB启动至NOMOUNT阶段，命令如下：  

```shell
# 场景1：从关闭状态启动PDB至NOMOUNT阶段
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2 -m nomount
# 场景2：从任意状态一键重启PDB至NOMOUNT阶段
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2 -m nomount
```

== 连接根容器执行SQL语句

使用全局DBA用户登录根容器，执行ALTER PLUGGABLE DATABASE语句将PDB启动至NOMOUNT阶段。

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 NOMOUNT;
```

== 使用ycsctl工具

仅适用于共享集群/分布式集群部署的容器数据库。

以数据库安装用户登录数据库所在服务器，使用ycsctl工具将PDB启动至NOMOUNT阶段，命令如下：  

```shell
# 从关闭状态启动PDB至NOMOUNT阶段
$ ycsctl start pdb -db yashancdb -pdb pdb1 -startoption nomount
```
:::

PDB启动至NOMOUNT阶段后STATUS变为STARTED，连接根容器或直连目标PDB均可查看。  

```shell
# 连接根容器
$ yasql sys/********@192.168.1.2:1688
# 直连PDB
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             STARTED
                    3 PDB2                                                             STARTED
```

### 启动至MOUNT阶段

PDB可以从关闭状态直接启动至MOUNT阶段，也可以从NOMOUNT阶段逐步启动至MOUNT阶段，操作方式分为以下几种：  

::: tabs
== 使用yasboot工具

在共享集群/分布式集群部署的容器数据库中，只能将目标PDB从关闭状态启动至MOUNT阶段。

```shell
# 场景1：从关闭状态或NOMOUNT阶段启动PDB至MOUNT阶段
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2 -m mount
# 场景2：从任意状态一键重启PDB至MOUNT阶段
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2 -m mount
```

== 连接根容器执行SQL语句

只能将目标PDB从关闭状态或NOMOUNT阶段启动至MOUNT阶段。

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 MOUNT;
```

== 直连目标PDB执行SQL语句

须确保目标PDB已处于NOMOUNT阶段。

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> ALTER DATABASE MOUNT;
```

== 使用ycsctl工具

仅适用于共享集群/分布式集群部署的容器数据库，且只能将目标PDB从关闭状态启动至MOUNT阶段。

```shell
$ ycsctl start pdb -db yashancdb -pdb pdb2 -startoption mount
```
:::

PDB启动至MOUNT阶段后STATUS更新为MOUNTED，连接根容器或直连目标PDB均可查看。  

```sql
SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             MOUNTED
                    3 PDB2                                                             MOUNTED
```

### 启动至OPEN阶段

PDB可以从关闭状态、NOMOUNT阶段或MOUNT阶段启动至OPEN阶段，操作方式分为以下几种：  

::: tabs
== 使用yasboot工具

使用yasboot工具启动PDB至OPEN时，无法指定其运行模式。

在共享集群/分布式集群部署的容器数据库中，只能将目标PDB从关闭状态启动至MOUNT阶段。

```shell
# 场景1：从关闭状态、NOMOUNT阶段或MOUNT阶段启动PDB至OPEN阶段
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2
# 场景2：从任意状态一键重启PDB至OPEN阶段
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2
```

== 连接根容器执行SQL语句

连接根容器执行SQL语句启动PDB至OPEN时，无法指定其运行模式。

须确保目标PDB未处于OPEN状态。

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 OPEN;
```

== 直连目标PDB执行SQL语句

须确保目标PDB已处于NOMOUNT或MOUNT阶段。

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> ALTER DATABASE OPEN READWRITE;
```

== 使用ycsctl工具

仅适用于共享集群/分布式集群部署的容器数据库，且只能将目标PDB从关闭状态启动至OPEN阶段。

```shell
# 不指定-node参数则默认运行于集群中所有服务器上

$ ycsctl start pdb -db yashancdb -pdb pdb3 -startoption open
```
:::

PDB启动至OPEN阶段后STATUS变为OPEN，连接根容器或直连目标PDB均可查看。  

```shell
# 连接根容器
$ yasql sys/********@192.168.1.2:1688
# 直连PDB
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN
                    3 PDB2                                                             OPEN 
```

<span id="close" name="close"></span>

## 关闭PDB

关闭PDB可以使用以下方法：

| 方法 | 功能说明 |
| -------------------------------- | -------------------------------------------- |
| [yasboot工具](../../../工具手册/yasboot/yasboot命令介绍/yasboot pdb.md) | 可以单个或批量关闭PDB，但单机部署中无法以ABORT关库模式，共享集群/分布式集群部署中无法指定关库模式。 |
| 连接根容器执行[ALTER PLUGGABLE DATABASE](../../../开发手册/SQL参考手册/SQL语句/ALTER PLUGGABLE DATABASE.md)语句 | 可以单个或批量关闭PDB，但无法以ABORT模式关闭PDB。 |
| 直连目标PDB执行[SHUTDOWN](../../../开发手册/SQL参考手册/SQL语句/SHUTDOWN)语句 | 仅对执行PDB生效。 |
| [ycsctl工具](../../../工具手册/ycsctl/ycsctl使用指导/数据库资源管理命令.md)                                               | 仅适用于共享集群/分布式集群部署的容器数据库。<br />单次可以关闭1个PDB的实例，但无法指定PDB的关库模式。<br/>仅关闭执行服务器节点上目标PDB的实例。 |

::: tabs
== 使用yasboot工具

使用yasboot工具关闭PDB时，单机部署中无法指定为ABORT模式，共享集群/分布式集群部署中无法指定关库模式。  

```shell
# 不指定关库模式
$ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb1,pdb2

# 单机部署中，可指定关库模式为NORMAL或IMMEDIATE，省略则默认为NORMAL
$ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb1,pdb2 -s normal
```

== 连接根容器执行SQL语句

连接根容器执行SQL语句关闭PDB时，关库模式只能指定为NORMAL或IMMEDIATE，省略则默认为NORMAL。

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 CLOSE;
```

== 直连目标PDB执行SQL语句

直连目标PDB执行SQL语句关闭PDB时，可以指定为任意关库模式，省略则默认为NORMAL。

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> SHUTDOWN NORMAL;
SQL> SHUTDOWN IMMEDIATE;
SQL> SHUTDOWN ABORT;
```

== 使用ycsctl工具

仅适用于共享集群/分布式集群部署的容器数据库，且无法指定PDB的关库模式。

```shell
$ ycsctl stop pdb -db yashancdb -pdb pdb1
```
:::
