## 通用描述

BUILD DATABASE用于在主备高可用部署环境中构建备库。

该功能不适用于存算一体分布式集群部署。

在存在级联备的环境中，级联备库的上级备库对应为下文中的主库，级联备库对应为下文中的备库，具有与主备库相同的语法、功能及约束。

> **Note**:
>
> 在存在级联备的环境中，级联备库的上级备库对应为下文中的主库，级联备库对应为下文中的备库，具有与主备库相同的语法、功能及约束。



当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，应根据待构建的对象选择BUILD DATABASE语句的执行容器：

- 连接根容器

    - 执行BUILD DATABASE：构建整个备容器数据库（含根容器和所有PDB）

    - 执行BUILD DATABASE ROOT：构建备根容器

- 直连PDB执行BUILD DATABASE：仅构建对应的备PDB

  另外，构建备PDB还可以连接根容器执行[BUILD PLUGGABLE DATABASE](./BUILD PLUGGABLE DATABASE)进行全局运维。



在主库中执行该语句时，要求用户为SYS超级用户或拥有SYSDBA、SYSBACKUP权限的用户。

执行BUILD操作时系统会先自动进行环境预检查，检查项目包括待恢复文件路径是否可达、目标地址空余磁盘空间是否充足。检查通过再继续BUILD，检查失败则直接退出。

对备库成功执行本语句的前提如下：

*   主库已OPEN，并处于归档模式。
* 需先确保主库不处于最大保护模式（MAXIMIZE PROTECTION），可查询V$DATABASE视图的PROTECTION_MODE字段确认。如果是MAXIMIZE PROTECTION，必须先[切换](./ALTER DATABASE.md#set_standby_clause)为其他模式再发起BUILD，BUILD完成后可按需切换回最大保护模式。
*   备库服务器上已安装YashanDB，且启动实例到NOMOUNT状态（增量BUILD中的备库须为OPEN状态）。
*   主备库上网络及权限畅通，相关配置参数值已正确设置。
*   满足[CREATE DATABASE](./CREATE DATABASE)语句的所有约束条件。

本语句涵盖了YashanDB需要进行BUILD操作的各种场景：

- **全量BUILD**：用于新建或重建备库的初始化

  - 由备库分别对自身发起BUILD（[BUILD DATABASE](#builddatabase)）：适用于单机/共享集群/分布式集群部署
  - 由主库向备库发起BUILD（[BUILD DATABASE TO STANDBY](#builddatabasetostandby)指定单个standby_name或[BUILD DATABASE TO REMOTE](#builddatabasetoremote)指定单个IP）：适用于单机/共享集群/分布式集群部署
  - 由主库向多备库同时发起BUILD（[BUILD DATABASE TO STANDBY](#builddatabasetostandby)或[BUILD DATABASE TO REMOTE](#builddatabasetoremote)）：适用于单机部署
  - 由备库向多备库同时发起BUILD（[BUILD DATABASE TO REMOTE](#builddatabasetoremote)）：适用于单机部署

- **增量BUILD**：用于异常备库的快速修复

  - 由主库向存在gap的备库发起BUILD（[BUILD DATABASE INCREMENTAL TO STANDBY](#builddatabaseincstandby)或[BUILD DATABASE INCREMENTAL TO REMOTE](#builddatabasetoremote)）：适用于单机部署
  - 由备库向存在gap的备库发起BUILD（[BUILD DATABASE INCREMENTAL TO REMOTE](#builddatabasetoremote)）：适用于单机部署
  - 由主库向need repair的备库发起BUILD（[BUILD DATABASE REPAIR STANDBY](#builddatabaserepstandby)）：适用于单机部署

本语句执行过程中，可以通过v$backup_progress视图查询某个BUILD操作的进度。若BUILD中断或失败，需注意：

- 由于执行本语句时，系统会将主库（或者某个备库）的控制文件、数据文件、redo日志文件和归档日志文件在线传输至备库的对应目录，过程中若出现错误将报错并中断操作，已传输的文件不会自动清除，再次运行本语句前，需在备库上手动清除脏数据。
- 对于同时发起的多备库并行BUILD，当某一个备库BUILD操作失败时，所有的备库BUILD操作都将失败。

## 语句定义

**build database::=**

```ebnf
= BUILD DATABASE (((INCREMENTAL TO ((STANDBY standby_name) | (REMOTE standby_address))) 
| (REPAIR STANDBY standby_name)) [DISCONNECT FROM SESSION]
| [(TO ((STANDBY '(' ((standby_name {',' standby_name })| '*')')') 
    | (REMOTE '(' standby_address {',' standby_address }')') ) ) 
    | ROOT ] 
  [SKIP VALIDATE] [OVERWRITE] ) 
[PARALLELISM integer] .
```

<span id="builddatabase" name="builddatabase"></span>

### BUILD DATABASE

该语句后面无任何选项时，表示本操作在备库上执行，将主库服务器文件在线传输到备库服务器。

示例（单机/共享集群/分布式集群部署）

```sql
--在某个备库上执行
BUILD DATABASE;
```

<span id="builddatabaseincstandby" name="builddatabaseincstandby"></span>

### BUILD DATABASE INCREMENTAL TO STANDBY

当备库与主库间存在较大gap时，在主库上通过[standby_name](#standbyname)指定备库执行本语句可以快速修复异常备库，缩小主备之间的差距。 

本操作每次只能指定一个备库，且指定的备库必须是一个正常open状态的备库（通过v$replication_status视图查看备库的状态）。

> **Caution**: 
>
> - 本操作会自动重启备库，清理控制文件，且BUILD过程中备库处于非open状态，谨慎使用。
> - 如操作过程中途失败，只能对备库进行全量BUILD修复。


示例（单机部署）
```sql
BUILD DATABASE INCREMENTAL TO STANDBY standby1_name;
```

<span id="builddatabaseincstandby" name="builddatabaseincstandby"></span>

### BUILD DATABASE INCREMENTAL TO REMOTE

当备库与主库间存在较大gap时，在主库上通过[standby_address](#standbyaddress)指定备库执行本语句可以快速修复异常备库，缩小主备之间的差距。 

为了避免影响主库的业务，也可以在某个正常的同步备库或者主备差距较小的备库上执行本语句。 

本操作每次只能指定一个IP地址（通过[standby_address](#standbyaddress)指定），且指定的备库必须是一个正常open状态的备库（通过v$replication_status视图查看备库的状态）。

> **Caution**: 
>
>- 本操作会自动重启备库，清理控制文件，且BUILD过程中备库处于非open状态，谨慎使用。
>- 如操作过程中途失败，只能对备库进行全量BUILD修复。

示例（单机部署）
```sql
BUILD DATABASE INCREMENTAL TO REMOTE '127.0.0.1:1689';
```

<span id="builddatabaserepstandby" name="builddatabaserepstandby"></span>

### BUILD DATABASE REPAIR STANDBY

当某个备库为need repair状态（通过v$replication_status视图查看备库的状态）时，在主库上通过[standby_name](#standbyname)指定备库执行本语句可以快速修复异常备库。

本操作每次只能指定一个备库，且指定的备库必须是某些异常类型（通过v$replication_status视图查看备库的异常类型）的need repair状态，例如主库归档被清理等，其他的异常只能通过全量BUILD修复，更详细的描述请查看高可用手册[修复异常备库](../../../高可用/修复异常备库)。

> **Note**: 
>
>- 如果只给主库执行备份恢复操作也会造成脑裂现象，此时只能通过全量BUILD重建备库，无法使用脑裂修复操作修复备库。

示例（单机部署）
```sql
BUILD DATABASE REPAIR STANDBY standby1_name;
```

<span id="builddatabasetostandby" name="builddatabasetostandby"></span>

### BUILD DATABASE TO STANDBY

本操作一般在主库上执行，用于指定多个不同备库，将主库服务器文件同时在线传输到这些备库服务器。

在共享集群/分布式集群部署中执行本操作时，只能指定1个备集群。

<span id="standbyname" name="standbyname"></span>

**standby_name**

指定备库的DB_UNIQUE_NAME（通过v$archive_dest视图获得该值），在一个HA集群中，DB_UNIQUE_NAME唯一标识了一台备库服务器。

多个standby_name之间以 `,` 分隔，且不可以重复。

**\***

指定与该主库连接的所有的备库。

示例（单机部署）

```sql
-- 指定对应的备库DB_UNIQUE_NAME执行BUILD。
BUILD DATABASE TO STANDBY (standby1_name, standby2_name);

-- 指定 * 符号即为该主库的所有备库执行BUILD。
BUILD DATABASE TO STANDBY (*);
```

示例（共享集群/分布式集群部署）

```sql
-- 指定对应的备集群DB_UNIQUE_NAME执行BUILD。
BUILD DATABASE TO STANDBY (standby1_name);
```

<span id="builddatabasetoremote" name="builddatabasetoremote"></span>

### BUILD DATABASE TO REMOTE

本操作可以在主库或备库上执行，用于指定多个不同IP，将本机服务器文件同时在线传输到这些服务器。

当某个备库出现异常时，为避免影响主库的业务，可以在一个正常同步备库上执行本语句，通过指定IP地址在线传输服务器文件到异常备库进行修复。 

在共享集群/分布式集群部署中执行本操作时，只能指定1个备集群。

<span id="standbyaddress" name="standbyaddress"></span>

**standby_address**

指定备库的IP地址，需为`address:port`格式的一个有效地址，例如`127.0.0.1:1689`。

多个standby_name之间以`,`指定，且不可以重复。

YashanDB支持指定一个未与主库建立复制链路的IP地址，此时主库仍可以连接该备库且执行BUILD操作（由于未建立链路，该备库只是创建standby库，而不会加入HA集群），但需遵循该主库所处HA集群环境的一切限制，例如指定的IP个数不能超过备库最大限制数量。

示例（单机部署）

```sql
BUILD DATABASE TO REMOTE ('127.0.0.1:2901', '127.0.0.1:2902');
```

示例（共享集群/分布式集群部署）

```sql
BUILD DATABASE TO REMOTE ('127.0.0.1:2901');
```

### BUILD DATABASE ROOT

该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），用于构建备根容器，一般在备根容器上执行，将主根容器的相关文件在线传输到本机。

示例（单机部署）

```sql
BUILD DATABASE ROOT;
```

### DISCONNECT FROM SESSION

本选项表示不需要等待BUILD操作完成之后再返回succeed，此时BUILD操作由后台线程完成，用户的会话线程不会阻塞。不指定本选项表示用户会话等待BUILD操作完成之后才返回succeed。



### SKIP VALIDATE

用于跳过默认的磁盘、文件等检查，具体检查项包含目标备库的待恢复文件路径是否可达、还原目标磁盘空间是否足够、是否存在同名文件等。

### OVERWRITE

用于覆盖同名文件，覆盖过程为先删除原有同名文件，在原址重建新文件。

不论是否指定该关键字，始终不会覆盖redo文件和归档文件，若存在此类同名文件需手动移走或清除。

### PARALLELISM integer

用于指定build database操作的并行度，integer的取值范围为`[1,16]`，默认的并行度为4。




示例（单机部署）

```sql
BUILD DATABASE TO STANDBY (standby1_name, standby2_name) DISCONNECT FROM SESSION;
```

示例（共享集群/分布式集群部署）

```sql
--在主集群上执行
BUILD DATABASE TO STANDBY (standby1_name) DISCONNECT FROM SESSION;
```
