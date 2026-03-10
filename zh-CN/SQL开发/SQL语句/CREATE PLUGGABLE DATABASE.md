## 通用描述

CREATE PLUGGABLE DATABASE用于创建一个可插拔数据库（PDB）。



该语句的适用范围如下：

- 该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），且只能连接根容器执行。

- 该语句不适用于存算一体分布式集群部署。



执行该语句要求根容器处于OPEN状态。

## 语句定义

**create pluggable database::=**

```ebnf+diagram
syntax::= CREATE PLUGGABLE DATABASE pdb_name create_pdb_from_seed [COMPAT_MODE "=" (yashan|mysql)]
```

**[create_pdb_from_seed](#createpdbfromseed)**

```ebnf+diagram
syntax::= (file_name_convert|
ADMIN USER "admin_user_name" IDENTIFIED BY "password"|
default_tablespace|
(ARCHIVELOG|NOARCHIVELOG))
{" " (file_name_convert|
ADMIN USER "admin_user_name" IDENTIFIED BY "password"|
default_tablespace|
(ARCHIVELOG|NOARCHIVELOG))}
```

**[file_name_convert](#filenameconvert)**

```ebnf+diagram
syntax::= FILE_NAME_CONVERT "=" (NONE|"(" (("'" filename_pattern "'" "," "'" replacement_filename_pattern "'") {"," ("'" filename_pattern "'" "," "'" replacement_filename_pattern "'")}) ")")
```

**[default_tablespace](#defaulttablespace)**

```ebnf+diagram
syntax::= DEFAULT TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause]
```

### 1. pdb\_name

指定要创建的PDB的名称，不可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

<span id="createpdbfromseed" name="createpdbfromseed" class="yaslink"></span>

### 2. create\_pdb\_from\_seed

该语句用于根据种子容器创建一个标准PDB。

<span id="filenameconvert" name="filenameconvert" class="yaslink"></span>

#### 2.1. file\_name\_convert

该语句用于配置PDB的数据文件路径（后文将简化称为“PDB_DATA”）转换规则，系统会根据转换规则将新PDB的数据文件路径配置为目标路径。可省略，省略时使用默认路径。


如需为新建PDB规划自定义数据文件路径，在创建PDB前必须先完成对应路径的创建（以及权限配置），并在创建PDB时使用该语句完成路径转换。

| 部署形态| 默认PDB_DATA| 自定义PDB_DATA要求|
| ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| 单机部署                           | $YASDB_DATA/containers/{pdb_name} | 必须为本地路径，且数据库安装用户必须具备读写权限             |
| 共享集群/分布式集群部署            | +DG0/containers/{pdb_name}        | 必须为[YFS](../../全部手册/数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径 |

> **Note**:
>
> 若PDB创建失败后再次重试时仍需使用原计划的自定义PDB_DATA，重试前还需确保目标路径为空。



**NONE**

表示不指定转换规则，效果等同于省略file_name_convert子句。

**('filename_pattern','replacement_filename_pattern')**

- filename_pattern应指定为种子容器中的文件路径，种子容器的文件均存放在$YASDB_DATA/containers/PDB$SEED目录（单机部署）或+DG0/containers/PDB$SEED目录（共享集群/分布式集群部署）。

- replacement_filename_pattern应指定为替换字符。  

上述路径指定格式要求如下：

- 在单机部署中，可以采用绝对路径（例如`/data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/dbfiles`）或以`?`或`.`替代$YASDB_DATA的相对路径（例如`?/containers/PDB$SEED/dbfiles`或`./containers/PDB$SEED/dbfiles`）。

- 在共享集群/分布式集群部署中，必须为YFS完整路径（例如`+DG0/containers/PDB$SEED/dbfiles`）。

#### 2.2. ADMIN USER

该语句用于指定PDB的本地用户信息，可省略，省略则不创建本地用户。

若创建本地用户，该用户初始只具备登录权限（CREATE SESSION）。

<span id="defaulttablespace" name="defaulttablespace" class="yaslink"></span>

#### 2.3. default\_tablespace

该语句用于指定内置的USERS表空间（DEFAULT）的属性，可省略，省略则系统按缺省值创建USERS表空间。

语法规则同CREATE DATABASE中[tablespace_clause](CREATE DATABASE.html#tablespaceclause)。

#### 2.4. ARCHIVELOG|NOARCHIVELOG

该语句用于指定PDB是否开启归档模式，可省略，省略则跟随根容器的相应配置。

### 3. COMPAT\_MODE

该语句用于指定PDB的语法模式，可省略，省略则默认为yashan模式。  

- yashan模式：使用YashanDB的语法体系，PDB创建后无法切换为mysql模式。  

- [mysql模式](../../全部手册/mysql模式参考手册/mysql模式概述)：用户输入的SQL语句将优先按照MySQL的语法体系进行解析，适用于需要密切适配MySQL数据库的使用场景。  

示例（单机部署）

```sql
-- 指定转换路径
CREATE PLUGGABLE DATABASE pdb1
FILE_NAME_CONVERT=('?/containers/PDB$SEED','?/pdb1')
ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1
DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M;
```

示例（共享集群/分布式集群部署）

```sql
-- 指定转换路径
CREATE PLUGGABLE DATABASE pdb1
FILE_NAME_CONVERT=('+DG0/containers/PDB$SEED/dbfiles','+DG1/PDB1/dbfiles')
ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1
DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M;
```

示例（单机/共享集群/分布式集群部署）

```sql
-- 不指定转换路径
CREATE PLUGGABLE DATABASE yashancdb_pdb2
FILE_NAME_CONVERT=NONE
ADMIN USER sys_pdb2 IDENTIFIED BY sys_pdb2
DEFAULT TABLESPACE DATAFILE 'userspdb2' size 128M;

-- 开启归档模式
CREATE PLUGGABLE DATABASE yashancdb_pdb3
ADMIN USER sys_pdb3 IDENTIFIED BY sys_pdb3
DEFAULT TABLESPACE DATAFILE 'userspdb3' size 128M
ARCHIVELOG;

-- 指定语法模式为mysql
CREATE PLUGGABLE DATABASE yashancdb_pdb4
ADMIN USER sys_pdb4 IDENTIFIED BY sys_pdb4
DEFAULT TABLESPACE DATAFILE 'userspdb4' size 128M
ARCHIVELOG
COMPAT_MODE = mysql;
```
