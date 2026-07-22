## 通用描述

DROP DATABASE用于删除当前数据库，删除数据库所包含的如下持久化文件：

- 数据文件
- 切片文件
- redo日志文件（可通过指定KEEP LOGFILE关键字保留）
- 归档日志文件（默认保留，可通过指定INCLUDING ARCHIVELOG关键字一并删除）
- 控制文件
- 双写文件（共享集群/分布式集群部署中无此类文件）
- 闪回日志文件

> **Warn**:
>
> DROP DATABASE语句无法回滚，**请谨慎操作**。


当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，若连接根容器使用该语句，表示进行全局操作，即涵盖根容器和所有PDB。若直连某个PDB使用该语句，则会报错，只能连接根容器执行[DROP PLUGGABLE DATABASE](DROP PLUGGABLE DATABASE)语句删除某个PDB。


该语句不适用于存算一体分布式集群部署。


该语句的使用要求如下：

- 要求数据库的控制文件完整（控制文件组成员均存在且正确）。

- 要求数据库处于NOMOUNT阶段，此时只能由sys用户连接数据库。

- 共享集群/分布式集群部署中，要求在主实例（[GV$INSTANCE](../../../参考手册/系统视图/动态视图/GV$INSTANCE)视图中INSTANCE_ROLE=MASTER_ROLE）上执行本语句。

- 在容器数据库中，删除根容器时还要求已删除所有PDB（包括种子容器）。

## 语句定义

**drop database::=**

```ebnf
= DROP DATABASE [INCLUDING ARCHIVELOG | KEEP LOGFILE] [KEEP METADATA].
```

### INCLUDING ARCHIVELOG

该语句决定是否删除数据库的归档日志文件，缺省为不删除。

### KEEP LOGFILE

该语句决定是否保留redo日志文件，缺省为不保留。  

### KEEP METADATA

该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），决定是否保留执行容器（根容器、PDB）的元数据，缺省为不保留。  

示例（单机/共享集群/分布式集群部署）

```sql
# 启动实例到NOMOUNT
$ yasboot cluster restart -c yashandb -m nomount

# 以sys用户登录数据库
$ yasql / as sysdba

-- 删除数据库
SQL> DROP DATABASE;
-- 或
SQL> DROP DATABASE INCLUDING ARCHIVELOG;
-- 或
SQL> DROP DATABASE KEEP LOGFILE;
```
