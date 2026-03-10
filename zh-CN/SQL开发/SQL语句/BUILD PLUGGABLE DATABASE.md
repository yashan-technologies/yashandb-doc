## 通用描述

BUILD PLUGGABLE DATABASE用于在主备高可用部署环境中构建备PDB。



该语句的适用范围如下：

- 该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），且只能连接根容器执行。

- 该语句不适用于存算一体分布式集群部署。



执行该语句要求根容器处于OPEN状态、目标PDB处于关闭状态。

执行BUILD操作时系统会先自动进行环境预检查，检查项目包括待恢复文件路径是否可达、目标地址空余磁盘空间是否充足。检查通过再执行BUILD，检查失败则直接退出。

## 语句定义

**build pluggable database::=**

```ebnf+diagram
syntax::= BUILD PLUGGABLE DATABASE ( (pdb_name) {"," (pdb_name)}|ALL) [SKIP VALIDATE] [OVERWRITE] [PARALLELISM integer]
```

### 1. pdb\_name|ALL

指定待操作的PDB，多个名称间使用逗号（`,`）隔开，指定ALL则表示操作所有PDB。

通过[V$PDBS](../../全部手册/参考手册/系统视图/动态视图/V$PDBS)视图或yasql的`show pdbs`命令可以获取当前所有PDB信息。  



### 2. SKIP VALIDATE

用于跳过默认的磁盘、文件等检查，具体检查项包含目标备库的待恢复文件路径是否可达、还原目标磁盘空间是否足够、是否存在同名文件等。

### 3. OVERWRITE

用于覆盖同名文件，覆盖过程为先删除原有同名文件，在原址重建新文件。

不论是否指定该关键字，始终不会覆盖redo文件和归档文件，若存在此类同名文件需手动移走或清除。

### 4. PARALLELISM integer

用于指定BUILD PLUGGABLE DATABASE操作的并行度，integer的取值范围为`[1,16]`，默认的并行度为4。



示例（单机部署）

```sql
BUILD PLUGGABLE DATABASE pdb1;
```
