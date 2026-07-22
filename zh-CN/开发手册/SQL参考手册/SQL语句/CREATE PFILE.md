## 通用描述

CREATE PFILE用于覆盖更新参数文件（yasdb.ini），每个实例/节点的服务器参数文件相互独立。

覆盖更新参数文件的前提如下：

- 当前数据库实例/节点已启动至NOMOUNT阶段。

- 执行该语句的用户须为sys用户或拥有SYSDBA、SYSOPER或SYSBACKUP角色的其他用户。

- 当前实例/节点已存在服务器参数文件（yasdb.spfile）。

## 语句定义

**create pfile::=**

```ebnf
= CREATE PFILE FROM SPFILE.
```

### FROM SPFILE

基于当前正在使用的服务器配置参数文件（yasdb.spfile）覆盖更新已有的参数文件（yasdb.ini）。
