## 通用描述

DROP PLUGGABLE DATABASE用于删除PDB，同时会删除PDB所包含的持久化文件：

- 数据文件
- 切片文件
- redo日志文件（可通过指定KEEP LOGFILE关键字保留）
- 归档日志文件（默认保留，可通过指定INCLUDING ARCHIVELOG关键字一并删除）
- 控制文件
- 双写文件（共享集群/分布式集群部署中无此类文件）

> **Warn**:
>
> DROP PLUGGABLE DATABASE语句无法回滚，**请谨慎操作**。





该语句的适用范围如下：

- 该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），且只能连接根容器执行。

- 该语句不适用于存算一体分布式集群部署。



执行该语句要求根容器处于OPEN状态、目标PDB处于关闭状态。

## 语句定义

**drop pluggable database::=**

```ebnf+diagram
syntax::= DROP PLUGGABLE DATABASE (pdb_name|ALL) [INCLUDING ARCHIVELOG | KEEP LOGFILE] [KEEP METADATA]
```

#### 1. pdb\_name

指定待删除的PDB名称，单次只能指定一个PDB。  

#### 2. ALL

指定该关键字表示删除所有PDB，包括内置种子容器。

>**Warn**:
>
> 根容器必须拥有1个种子容器，当前无法自定义创建种子容器，因此DROP PLUGGABLE DATABASE ALL后数据库将不可用。**目前该操作仅作为删除根容器的准备工作，请谨慎操作**。

指定ALL进行删除操作时，部分PDB操作失败不会阻塞其他PDB的删除，但会提示错误信息。

### 3. INCLUDING ARCHIVELOG

该语句决定是否同时删除归档日志文件，缺省为不删除。

### 4. KEEP LOGFILE

该语句决定是否保留redo日志文件，缺省为不保留。  

### 5. KEEP METADATA

该语句决定是否保留PDB的元数据，缺省为不保留。  

示例（单机/共享集群/分布式集群部署）

```sql
-- 1. 查看PDB状态
show pdbs;

-- 2. 关闭目标PDB
ALTER PLUGGABLE DATABASE pdb1 CLOSE IMMEDIATE;

-- 3. 删除目标PDB
DROP PLUGGABLE DATABASE pdb1;
-- 或
DROP PLUGGABLE DATABASE pdb1 INCLUDING ARCHIVELOG;
-- 或
DROP PLUGGABLE  DATABASE pdb1 KEEP LOGFILE;
```
