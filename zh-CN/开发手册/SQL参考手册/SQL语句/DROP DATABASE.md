## 通用描述

DROP DATABASE用于删除当前数据库，删除数据库所包含的如下持久化文件：

- 数据文件
- 切片文件
- redo日志文件
- 归档日志文件（默认保留，可通过指定INCLUDING ARCHIVELOG关键字一并删除）
- 控制文件
- 双写文件（共享集群/分布式集群部署中无此类文件）
- 闪回日志文件


> **Note**: 
>
> 本语句只能由SYS用户在数据库处于NOMOUNT状态时执行，且要求数据库的控制文件完整（控制文件组成员均存在且正确）。
>
> 共享集群/分布式集群部署模式下执行该语句需保证执行节点角色为MASTER ROLE，且为NOMOUNT状态。
> 
> 执行完DROP DATABASE语句之后数据库会自行重启。

本语句不适用于存算一体分布式集群部署。

## 语句定义

**drop database::=**

```ebnf
= DROP DATABASE [INCLUDING ARCHIVELOG].
```

### INCLUDING ARCHIVELOG

该语句决定是否删除数据库的归档日志文件，缺省为不删除。

示例（单机/共享集群/分布式集群部署）

```sql
# 启动实例到NOMOUNT
$ yasboot cluster restart -c yashandb -m nomount

# 以sys用户登录数据库
$ yasql / as sysdba

-- 删除数据库
SQL> DROP DATABASE;
```
