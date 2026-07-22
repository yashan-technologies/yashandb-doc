## 通用描述

CREATE SPFILE用于为当前实例节点创建二进制的服务器参数文件（yasdb.spfile），每个实例/节点的服务器参数文件相互独立。

创建服务器参数文件的前提如下：

- 当前数据库实例/节点已启动至NOMOUNT阶段。

- 执行该语句的用户须为sys用户或拥有SYSDBA、SYSOPER或SYSBACKUP角色的其他用户。

- 当前实例已存在参数文件（yasdb.ini），且不存在正在使用中的服务器参数文件（yasdb.spfile），否则执行该语句时会提示错误。

## 语句定义

**create spfile::=**

```ebnf
= CREATE SPFILE FROM PFILE.
```

### FROM PFILE

基于当前正在使用的参数文件（yasdb.ini）生成服务器参数文件（yasdb.spfile），服务器参数文件将存放在$YASDB_DATA/config路径。

服务器参数文件创建后，下一次启动该数据库实例时才会使用yasdb.spfile进行参数初始化，且会优先使用yasdb.spfile，除非校验失败才会使用yasdb.ini。
