## 通用描述

ALTER DATABASE用于修改数据库的相关属性。

存算一体分布式集群部署中，仅支持如下语法：

- startup_clauses
- convert filename [including archivelog]
- archivelog/noarchivelog
- set standby database to
- convert to physical standby
- switchover
- failover
- exit upgrade
- add logfile
- drop logfile
- delete archivelog

推荐使用yasboot运维工具管理分布式集群，具体见[yasboot命令介绍](../../全部手册/工具手册/yasboot/yasboot命令介绍/00yasboot命令介绍)章节描述。 

语句定义
----

**alter database::=**

```ebnf+diagram
syntax::= ALTER DATABASE (startup_clauses|database_file_clauses|logfile_clauses|standby_database_clauses|upgrade_clauses|repair_database_clauses|delete_archivelog_clauses|double_write_file_clauses|supplemental_log_clauses|flashback_database_clauses|add_instance_clauses|remove_instance_clauses)
```

**[startup\_clauses](#startupclauses)::=**

```ebnf+diagram
syntax::= MOUNT|OPEN [READWRITE|RESETLOGS|UPGRADE|READONLY]
```

**[database\_file\_clauses](#databasefileclauses)::=**

```ebnf+diagram
syntax::= DATAFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause) | (OFFLINE [DROP]))

syntax::= TEMPFILE filename ( (AUTOEXTEND (OFF|ON [NEXT size_clauses] [MAXSIZE (UNLIMITED|size_clause)])) | (RESIZE size_clause))

syntax::= CONVERT FILENAME [INCLUDING ARCHIVELOG]
```

**[logfile_clauses](#logfileclauses)::=**

```ebnf+diagram
syntax::= (ARCHIVELOG|NOARCHIVELOG)|
    (SET STANDBY DATABASE TO MAXIMIZE (PERFORMANCE|PROTECTION|AVAILABILITY) [FORCE][TIMEOUT integer])|
    (ADD [STANDBY] LOGFILE [THREAD integer] "(" (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel]) {"," (filename SIZE size_caluse [BLOCKSIZE size_clauses] [PARALLEL parallel])}")")|
    (DROP [STANDBY] LOGFILE [THREAD integer] filename)|(CLEAR [UNARCHIVED] LOGFILE filename)
```

**[standby\_database\_clauses](#standbydatabaseclauses)::=**

```ebnf+diagram
syntax::= CONVERT TO PHYSICAL STANDBY
|SWITCHOVER
|FAILOVER (RESET ID integer)
|(RECOVER ((MANAGED STANDBY DATABASE (([UNTIL SCN integer][DISCONNECT FROM SESSION])|CANCEL))|(TO LOGICAL STANDBY ((KEEP IDENTITY)|db_name))))
|(REGISTER [OR REPLACE] ARCHIVELOG {"," filename})
|(START LOGICAL STANDBY APPLY [IMMEDIATE])
|(STOP LOGICAL STANDBY APPLY)
```

**[upgrade\_clauses](#upgradeclauses)::=**

```ebnf+diagram
syntax::= EXIT UPGRADE
```

**[repair\_database\_clauses](#repairdatabaseclauses)::=**

```ebnf+diagram
syntax::= CONVERT TO NORMAL
```

**[delete\_archivelog\_clauses](#deletearchivelogclauses)::=**

```ebnf+diagram
syntax::= DELETE ARCHIVELOG (ALL|UNTIL ((SEQUENCE integer [THREAD integer])|TIME date|SCN integer))[FORCE]
```

**[double\_write\_file\_clauses](#doublewritefileclauses)::=**

```ebnf+diagram
syntax::= DOUBLE_WRITE RESIZE FILE size_clauses
```

**[supplemental\_log\_clauses](#supplementallogclauses)::=**

```ebnf+diagram
syntax::= (ADD|DROP) SUPPLEMENTAL LOG (DATA ["(" (ALL|PRIMARY KEY) {"," (ALL|PRIMARY KEY)}")" COLUMNS] | TABLE TYPE "("(HEAP|TAC|LSC) {"," ( HEAP|TAC|LSC)}")")
```

**[flashback\_database\_clauses](#flashbackdatabaseclauses)::=**

```ebnf+diagram
syntax::= FLASHBACK [ON|OFF]
```

**[add\_instance\_clauses](#addinstanceclauses)::=**

```ebnf+diagram
syntax::= ADD INSTANCE [ "(" (logfiles_clause | UNDO TABLESPACE DATAFILE datafiles_clause) ")" ]
```

**[remove\_instance\_clauses](#removeinstanceclauses)::=**

```ebnf+diagram
syntax::= REMOVE INSTANCE (instance_id)
```

<span id="startupclauses" name="startupclauses" class="yaslink"></span>

### 1. startup\_clauses

该语句用于MOUNT和OPEN数据库，以便用户访问。

MOUNT是数据库挂载物理文件后的状态，OPEN是数据库打开后的状态，详细数据库启动介绍请参考[实例启停](../../全部手册/数据库管理/实例管理/实例启停)。 



数据库实例从NOMOUNT或MOUNT阶段启动到OPEN阶段时，支持READWRITE、READONLY、RESETLOGS和UPGRADE四种打开模式。

- **READWRITE**：数据库默认打开为READWRITE模式，该模式下数据库支持完整的事务读写操作，用于正式生产环境。

- **READONLY**：以只读模式打开数据库，限制数据库只读，不产生任何redo。
  
  - 共享集群部署时，无法以只读模式打开数据库。

  - 单机主备部署时，物理备库默认使用该方式打开数据库。

- **UPGRADE**：数据库升级时，升级工具yasboot使用此模式打开数据库，该模式下不允许建立新的会话连接，也不允许以该模式手动OPEN数据库。

- **RESETLOGS**：当数据库进行了PITR（基于时间点的恢复）、数据库闪回或逻辑备库配置时，如果无法进行完全恢复，则需要通过RESETLOGS模式打开数据库，该模式将重新设置redo日志号。



示例

```sql
ALTER DATABASE MOUNT;
 
ALTER DATABASE OPEN;

ALTER DATABASE OPEN READWRITE;
```

<span id="databasefileclauses" name="databasefileclauses" class="yaslink"></span>

### 2. database\_file\_clauses

该语句用于对数据库的数据文件进行自动扩展的开关控制、大小指定等。此操作需要数据库处于OPEN状态。

在设置数据文件自动扩展或RESIZE数据文件时，对于TEMP表空间和SWAP表空间的数据文件使用tempfile选项，其它表空间的数据文件使用datafile选项。

#### 2.1. AUTOEXTEND OFF

关闭某个数据文件的自动扩展，同时该数据文件的NEXT_SIZE和MAX_SIZE被设置为0。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND OFF;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND OFF;
```

#### 2.2. AUTOEXTEND ON

开启某个数据文件的自动扩展，同时NEXT用于指定自动扩展下一空间的大小，以Bytes为单位，未指定则取默认值8K个BLOCK大小；MAXSIZE用于指定自动扩展的最大空间，UNLIMITED为无限制，未指定则默认为64M个BLOCK大小。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
ALTER DATABASE TEMPFILE '?/dbfiles/swap' AUTOEXTEND ON NEXT 8M MAXSIZE 64M;
```

#### 2.3. RESIZE

重新指定某个数据文件的大小。

此功能仅适用于单机部署。

创建表空间下的数据文件时可以通过设定数据文件自动扩展来满足用户对于更大数据文件的需求，但如果存储空间并不充足，或者数据文件按预期指定过大而实际使用很小，或者数据文件中存在大量被删除的临时数据而空间并没有被回收，这些情况下就需要使用resize的方式对数据文件进行缩小，释放磁盘空间。

使用resize对数据文件进行扩大，一次设置文件为自己所需大小 ，也可以避免频繁的申请资源。

resize后的数据文件大小必须在128到64MB个BLOCK之间。

resize操作不一定会成功，例如在扩大文件时可能会存在磁盘空间不足，或者在缩小文件时，当前数据文件的有效数据大小已超过指定的值。

UNDO表空间的数据文件只能进行扩大，不能进行缩小。

在共享集群/分布式集群部署中，本地TEMP/SWAP表空间的数据文件大小调整无法跨实例操作，即只能RESIZE当前实例所在服务器上的本地文件。 

示例（单机部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users' RESIZE 1048576;
ALTER DATABASE TEMPFILE '?/dbfiles/temp' RESIZE 1048576;
```

#### 2.4. OFFLINE [DROP]

改变某个数据文件至offline状态，可以在数据库处于MOUNT或者OPEN时执行该操作。

> **Warn**:
> 
> 执行该操作后，此数据文件所属的表空间将被置为OFFLINE状态，该表空间内的其他数据文件全被置为RECOVER状态。

共享集群部署中不允许执行offline_clause。

执行offline的场景：

*   存在数据文件损坏或丢失导致无法打开数据库，此类场景只能在数据库处于MOUNT时执行offline。
*   表空间的数据文件在有数据时无法被删除，但不再需要使用该数据文件。
*   需要数据隔离，临时将某个数据文件offline。

执行offline的说明：

*   尽量在数据库处于OPEN状态时执行此操作，除非存在数据文件损坏或丢失导致无法打开数据库。
*   该数据文件后续可能会被online且仍属于数据库，因此仍需要对其进行备份，但如果该数据文件名称被修改，会由于备份时找不到数据文件而导致备份失败。
*   发现数据库为abnormal状态时，查询V$DIAG_INCIDENT排查是否因文件损坏导致abnormal，如果是则可以将该数据文件offline。
*   在数据库处于MOUNT状态时执行此操作的额外说明：
    *   某些情况下（例如open过程中回放了创建该文件所属表空间的redo日志），数据库会修复已被此操作offline的数据文件和其所属表空间，并将它们置为online。
    *   文件被offline，然后打开数据库后，查询V$DATAFILE将无法正常显示该文件的创建时间，除非其被重新online。
    *   在备库上执行该操作，然后打开数据库后，备库将变成need repair状态。尽量在主库上执行该操作，这样可以通过redo日志同步使备库offline相同的文件。

执行offline的限制：

*   每次只能offline一个数据文件。
*   内置表空间的数据文件不能被offline。
*   数据文件被offline后不可读写，且不能对该文件进行resize或设置自动扩展开关等操作。
*   主库在mount和open状态均可执行该操作，备库只能在mount状态下执行。

##### 2.4.1. DROP

在数据库未开启归档模式时，执行数据文件offline必须指定此选项。在开启归档模式的情况下，此选项被忽略，即OFFLINE DROP = OFFLINE。

示例（单机）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE;

--非归档模式下只能用如下语句offline
ALTER DATABASE DATAFILE '?/dbfiles/file' OFFLINE DROP;
```

#### 2.5. CONVERT FILENAME [INCLUDING ARCHIVELOG]

当数据库整库迁移到其它目录后，该语法可用于将数据库记录在控制文件中的路径进行转换，从而使数据库继续正常启动、运行。该语句仅在实例处于NOMOUNT阶段时使用。

该语句的具体使用步骤如下：

1. 更新启动路径：修改配置参数文件中的控制文件路径参数CONTROL_FILES为当前数据库启动路径，或删除该参数的已有配置从而使用默认配置启动。
2. （可选）如需更新归档日志存储路径：修改配置参数文件中的归档日志路径参数ARCHIVE_LOCAL_DEST为当前数据库指定归档路径，或删除该参数的已有配置从而使用默认配置存储归档日志。
3. 配置数据文件路径转换参数DB_FILE_NAME_CONVERT，将旧的文件路径转换为新的文件路径。同时，需确认数据库是否需要开启双写，如需开启双写，请确保数据文件转换路径参数可以对双写文件路径生效。
4. 配置在线日志文件的路径转换参数REDO_FILE_NAME_CONVERT，将旧的文件路径转换为新的文件路径。
5. 配置DATABUCKET的路径转换参数DB_BUCKET_NAME_CONVERT，将旧的文件路径转换为新的文件路径。
6. 启动数据库至NOMOUNT状态，并根据是否需要转换归档日志路径从而决定是否显式指定子句INCLUDING ARCHIVELOG。
7. 启动数据库至OPEN状态。

##### 2.5.1. INCLUDING ARCHIVELOG

当需要转换归档日志路径时指定该子句。

示例

```sql
ALTER DATABASE CONVERT FILENAME;

包含归档日志的路径转换使用如下语句
ALTER DATABASE CONVERT FILENAME INCLUDING ARCHIVELOG;
```

<span id="logfileclauses" name="logfileclauses" class="yaslink"></span>

### 3. logfile\_clauses

该语句用于对数据库的redo日志进行归档模式设置、备库保护模式设置、增加、删除等操作。

#### 3.1. ARCHIVELOG/NOARCHIVELOG

启用或停止数据库的日志归档模式。

单机部署中，此操作需要数据库实例处于MOUNT状态。

共享集群/分布式集群部署中，此操作需要当前所在实例处于MOUNT状态，且其他实例处于NOMOUNT状态。

当数据库处于主备复制模式（单机主备部署或主备集群部署）时，无法从归档模式切换为非归档模式。

示例

```sql
ALTER DATABASE ARCHIVELOG;
 
ALTER DATABASE NOARCHIVELOG;
```

<span id="set_standby_clause" name="set_standby_clause" class="yaslink"></span>

#### 3.2. SET STANDBY DATABASE TO

指定备库的保护模式，默认值为MAXIMIZE PERFORMANCE，保护模式分为以下3种：

*   MAXIMIZE PERFORMANCE：最大性能模式，主库事务提交无需等待备库收到日志，保证了主数据库的可用性及性能，但主库宕机后可能丢失数据。

*   MAXIMIZE PROTECTION：最大保护模式，备库的数据保护优先于主库的可用性，默认情况下主库的日志在同步备库且备库落盘后事务才能提交（若COMMIT_WAIT = NOWAIT则主库事务提交事无需等待日志落盘）。若同步备库故障，主库会在一段时间后变为只读模式。

*   MAXIMIZE AVAILABILITY：最大可用模式，同步备库正常时，默认情况下主库的日志在同步备库上落盘后事务才能提交（若COMMIT_WAIT = NOWAIT则主库事务提交事无需等待日志落盘）；同步备库故障时，事务提交也不会阻塞，保证数据库可用。

> **Note**: 
>
> 在共享集群/分布式集群部署下，设置最大保护模式要求所有存活实例处于OPEN状态。
>
> 在共享集群/分布式集群部署下，无法使用TIMEOUT字段。

**FORCE**

切换为MAXIMIZE PROTECTION的前提是主库的日志已经同步到备库，否则会报错。指定FORCE关键字表示强制设置，忽略该报错。

**TIMEOUT**

TIMEOUT关键字表示在设置最大保护模式时，等待备库同步的时间，超过该时间将报错，单位为秒，可省略，默认为10s。

示例

```sql
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PERFORMANCE;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE AVAILABILITY;
 
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION FORCE;
```

示例（单机、存算一体分布式集群部署）

```sql
ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PROTECTION TIMEOUT 100;
```

<span id="add_logfile" name="add_logfile" class="yaslink"></span>

#### 3.3. ADD LOGFILE

为数据库增加新的redo日志，同时增加多个文件以','隔开。此操作需要数据库处于OPEN阶段。

在共享集群/分布式集群部署中，redo日志文件是各个实例独享的，各个实例可以给自己添加redo日志文件，主实例则可以通过指定THREAD integer为离线的实例添加redo日志文件。

##### 3.3.1. STANDBY

指定STANDBY子句，添加redo日志文件到standby redo log，如果不指定该字段，则添加一个redo文件到online redo log。

##### 3.3.2. filename

指定新增redo日志文件的文件名。

##### 3.3.3. SIZE

指定新增redo日志文件大小。

redo日志文件大小的最小值受DB_BLOCK_SIZE，MAX_SESSIONS和REDO_BUFFER_SIZE参数影响，最小值参考公式为`DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2`。

##### 3.3.4. BLOCKSIZE

redo日志文件的块大小，默认为4096，可手动指定为512的整数。redo日志实际创建的文件大小是BLOCKSIZE的整数倍，若指定大小不是整数倍，则实际创建大小向上取整。

##### 3.3.5. PARALLEL

指定创建redo日志文件的并行度，取值范围为1到8。不指定时系统根据文件大小自适应并行度，例如文件不超过1G时的并行度为1，文件超过128G时的并行度为8，文件大小在1G到128G之间时的并行度为4。

示例

```sql
ALTER DATABASE ADD LOGFILE ('?/dbfiles/redo5' SIZE 72355840,'?/dbfiles/redo6' SIZE 72355840);
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo6' SIZE 72355840 BLOCKSIZE 512;
ALTER DATABASE ADD LOGFILE '?/dbfiles/redo7' SIZE 72355840 PARALLEL 4;
ALTER DATABASE ADD STANDBY LOGFILE '?/dbfiles/redo8' SIZE 72355840 BLOCKSIZE 512;
```

示例（共享集群/分布式集群部署）

```sql
-- 假定实例2已退出集群，主实例可以给实例2添加redo文件
ALTER DATABASE ADD LOGFILE THREAD 2 '?/dbfiles/redo27' SIZE 72355840 PARALLEL 4;

ALTER DATABASE ADD STANDBY LOGFILE THREAD 2 '?/dbfiles/redo28' SIZE 72355840 BLOCKSIZE 512;
```

<span id="drop_logfile" name="drop_logfile" class="yaslink"></span>

#### 3.4. DROP LOGFILE

删除一个已存在的redo日志，对于正在使用中的redo日志则不被允许删除。此操作需要数据库处于OPEN状态。

在共享集群/分布式集群部署中，redo日志文件是各个实例独享的，各个实例可以给自己删除redo日志文件，主实例则可以通过指定THREAD integer为离线的实例删除redo日志文件，其中integer为实例ID。

##### 3.4.1. STANDBY

指定STANDBY子句，删除一个standby redo log，如果不指定该字段，则删除一个online redo log。

示例

```sql
ALTER DATABASE DROP LOGFILE '?/dbfiles/redo5';
ALTER DATABASE DROP STANDBY LOGFILE '?/dbfiles/redo8';
```

示例（共享集群/分布式集群部署）

```sql
-- 假定实例2已退出集群，主实例可以给实例2删除redo文件
ALTER DATABASE DROP LOGFILE THREAD 2 '?/dbfiles/redo27';

ALTER DATABASE DROP STANDBY LOGFILE THREAD 2 '?/dbfiles/redo28';
```

#### 3.5. CLEAR LOGFILE

重新初始化在线redo文件，如果redo文件不存在或者被误删，该语句会重新创建该文件，常用于redo文件损坏、文件丢失等异常场景处理。

##### 3.5.1. UNARCHIVED

redo文件不等待归档直接初始化。

> **Caution**: 
> 
> 指定UNARCHIVED字段可能存在如下风险，请谨慎使用。
> - 可能导致旧的备份集不可用，建议执行一次备份。
> - 可能导致备库不可用，后续可通过build database修复，请查阅[修复异常备库](../../全部手册/高可用/修复异常备库)。

示例

```sql
ALTER DATABASE CLEAR LOGFILE '?/dbfiles/redo5';
```

<span id="standbydatabaseclauses" name="standbydatabaseclauses" class="yaslink"></span>

### 4. standby\_database\_clauses

该语句用于执行主备库之间的切换。主备库详细操作描述请参考[高可用](../../全部手册/高可用/YashanDB高可用概述)。

#### 4.1. CONVERT TO PHYSICAL STANDBY

从主数据库切换为备数据库。

示例

```sql
ALTER DATABASE CONVERT TO PHYSICAL STANDBY;
```

> **Note**: 
>
> - 数据库的角色必须是PRIMARY，且执行实例必须处于MOUNT状态。
> - 在共享集群/分布式集群部署下，只有1号实例并且该实例是MASTER_ROLE才可以执行该操作。

#### 4.2. SWITCHOVER

从备数据库切换回主数据库。

示例

```sql
ALTER DATABASE SWITCHOVER;
```

> **Note**: 
>
> - 数据库的角色必须是STANDBY，且执行实例必须处于OPEN状态。
> - 主库的所有存活实例的状态必须是OPEN状态。
> - 主备之间的redo传输链路连接正常，且备库处于NORMAL状态。（主库查询视图V$ARCHIVE_DEST_STATUS，备库查询视图V$REPLICATION_STATUS）
> - 共享集群/分布式集群部署下，主库的1号实例必须存活。
> - 共享集群/分布式集群部署下，switchover的性能受checkpoint的影响，需要等待非1号实例完成全量checkpoint再执行该操作。

<span id="failover" name="failover" class="yaslink"></span>

#### 4.3. FAILOVER

当主库出现故障不能恢复时，将备库强制切换为主库。

示例

```sql
ALTER DATABASE FAILOVER;
```

> **Note**: 
>
> - 数据库的角色必须是STANDBY。
> - 执行实例必须处于OPEN状态。
> - 数据库与主数据库的连接必须是断开的，可以查看视图V$REPLICATION_STATUS查看主备的连接情况。
> - 共享集群/分布式集群部署下，需在备集群的master实例执行该操作，备集群的其他存活实例保持OPEN状态即可。
> - RESET ID仅用于仲裁选主场景yasom自动下发的failover语句中，**不得手动指定该选项**。

#### 4.4. RECOVER MANAGED STANDBY DATABASE CANCEL

在备库并且为Open状态下执行的SQL语句，该语句的作用为停止当前回放操作。

如果有前台回放正在进行，则中断该线程使其退出。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
```

#### 4.5. RECOVER MANAGED STANDBY DATABASE

在备库并且为Open状态下执行的SQL语句，该语句的作用为启动备库回放，如需退出，需要执行取消回放SQL语句。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE;
```

##### 4.5.1. UNTIL SCN integer

在备库并且为Open状态下执行的SQL语句，该语句的作用为启动备库回放，回放到指定到SCN时主动退出。

此时如需退出则需要执行取消回放SQL语句进行中断操作。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123;
```

##### 4.5.2. DISCONNECT FROM SESSION

在备库并且为Open状态下执行的SQL语句，该语句的作用为在后台执行回放，当前会话可执行其他业务。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE UNTIL SCN 123123123 DISCONNECT FROM SESSION;
```

#### 4.6. TO LOGICAL STANDBY

该语句用于将物理备用数据库转换为逻辑备用数据库。

##### 4.6.1. KEEP IDENTITY

如果您想要使用逻辑备用数据库提供的滚动升级功能，同时恢复到主数据库和物理备用数据库的原始配置，请使用此子句。
> **Note**: 
>
> 该子句仅用于滚动升级场景，请勿使用该子句创建通用的逻辑备用数据库。

##### 4.6.2. db\_name

指定数据库名称用以标识新的逻辑备用数据库。

通过该方式构建逻辑备用数据库后，需要执行ALTER DATABASE OPEN RESETLOGS操作。

#### 4.7. REGISTER ARCHIVELOG

该语句用于手动注册归档。该SQL的功能约束有：

* RESTORE DATABASE后且数据库未open，可以用该SQL手动注册归档。
* 数据库恢复或创建完整后，此操作的对象必须是备库，并且配置参数SANDBOX_STANDBY为TRUE。
* 指定的归档的路径可以为绝对路径，也可以为文件名，使用文件名时默认路径为归档路径（配置参数ARCHIVE_LOCAL_DEST）。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE REGISTER ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC';
ALTER DATABASE REGISTER ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC', '/home/yashan/archive/arch_0_2.ARC';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';

-- 结合RESTORE DATABASE使用
RESTORE DATABASE FROM 'BAK1';
ALTER DATABASE REGISTER ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';
RECOVER DATABASE;
ALTER DATABASE OPEN;
```

#### 4.8. OR REPLACE

如果发现归档已经注册，就替换原有注册的归档，该操作比较危险，需要谨慎使用。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG '/home/yashan/archive/arch_0_1.ARC', '/home/yashan/archive/arch_0_2.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC';
ALTER DATABASE REGISTER OR REPLACE ARCHIVELOG 'arch_0_1.ARC', 'arch_0_2.ARC';
```

#### 4.9. START LOGICAL STANDBY APPLY

该语句用于开启逻辑备用数据库的逻辑回放功能，该功能默认关闭。

#### 4.10. STOP LOGICAL STANDBY APPLY

该语句用于有序停止逻辑备库的日志回放服务。

不适用于物理备用数据库。

<span id="upgradeclauses" name="upgradeclauses" class="yaslink"></span>

### 5. upgrade\_clauses

该语句用于数据库的版本升级。

#### 5.1. EXIT UPGRADE

当数据库升级完成之后，可以直接退出升级模式进入正常OPEN模式，无需重启。

示例

```sql
ALTER DATABASE EXIT UPGRADE;
```

<span id="repairdatabaseclauses" name="repairdatabaseclauses" class="yaslink"></span>

### 6. repair\_database\_clauses

该语句用于数据库处于ABNORMAL状态时，DBA介入修复。

#### 6.1. CONVERT TO NORMAL

当数据库出现故障时，数据库被设为只读，数据库为故障状态，DBA修复之后，可以通过本语句将数据库手动切换为正常模式。

> **Note**: 
>
> 当数据库因为资源错误陷入异常状态时，无法使用此语句将数据库状态置为正常。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE CONVERT TO NORMAL;
```

<span id="deletearchivelogclauses" name="deletearchivelogclauses" class="yaslink"></span>

### 7. delete\_archivelog\_clauses

该语句用于对数据库的归档文件进行手动清理，释放磁盘空间。

手动清理归档日志的筛选条件由ARCHIVELOG_DELETION_POLICY或ARCH_CLEAN_IGNORE_MODE参数决定，清理条件的具体描述请查阅[归档管理](../../全部手册/数据库管理/实例管理/归档管理)。 

#### 7.1. DELETE ARCHIVELOG 

##### 7.1.1. ALL

清理掉满足清理条件的所有归档。

##### 7.1.2. UNTIL SEQUENCE integer [THREAD integer]

清理指定序列号之前满足清理条件的归档。若不指定实例，则默认清理实例1的归档。

##### 7.1.3. UNTIL TIME date

清理指定时间之前生成的并且满足清理条件的归档。

##### 7.1.4. UNTIL SCN integer

清理指定SCN之前生成的并且满足清理条件的归档。（同V$ARCHIVED_LOG中的NEXT_CHANGE#作比较）。

###### 7.1.4.1. FORCE 

不考虑清理条件，强制清理归档。

示例

```sql
ALTER DATABASE DELETE ARCHIVELOG ALL;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME TO_DATE('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss');

--强制归档清理
ALTER DATABASE DELETE ARCHIVELOG ALL FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL SEQUENCE 5 FORCE;

ALTER DATABASE DELETE ARCHIVELOG UNTIL TIME TO_DATE('2022-06-01 18:00:00', 'yyyy-mm-dd hh24:mi:ss') FORCE;
```

<span id="doublewritefileclauses" name="doublewritefileclauses" class="yaslink"></span>

### 8. double\_write\_file\_clauses

该语句用于重新指定双写文件的大小。

共享集群/分布式集群部署中，不存在双写文件。

存算一体分布式集群部署中，不允许调整双写文件的大小。

示例（单机部署）

```sql
ALTER DATABASE DOUBLE_WRITE RESIZE FILE 32M;
```

<span id="supplementallogclauses" name="supplementallogclauses" class="yaslink"></span>

### 9. supplemental\_log\_clauses

该语句用于配置数据库级别的附加日志，不适用于存算一体分布式集群部署。

开启附加日志后，数据库将在redo里额外记录一些数据，这些数据包括DDL的原始SQL文本，update，delete时用于定位行位置的索引信息等。

结合附加日志，可以通过redo解析还原出对应的DDL、DML语句，通常用于异构数据库同步。

数据库级附加日志对选定类型的所有用户表生效，可以通过动态视图[V$DATABASE](../../全部手册/参考手册/系统视图/动态视图/V$DATABASE)查看数据库级别的附加日志生效状态。若目标表开启了[加密](./CREATE TABLE.html#columnencryptionclause)，数据库级附加日志仍会根据选定表类型显示生效状态但实际redo里并不会记录该表相关的附加信息。

表级附加日志请参考[ALTER TABLE](./ALTER TABLE.html#addsupplementalloggingclause)。

如需使用[YStream服务](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_YSTREAM_ADM)，应先开启附加日志（库级或表级）再创建YStream服务，若先创建了YStream服务再开启附加日志会报错。

#### 9.1. SUPPLEMENTAL LOG DATA

SUPPLEMENTAL LOG DATA表示最小附加日志，这种模式下redo里会额外记录DDL文本和DML的rowid，性能影响最小。

当开启ALL或PRIMARY KEY模式的附加日志时，最小附加日志隐式开启。且在关闭其他模式的附加日志前，不能关闭最小附加日志。

ALL模式的优先级大于PRIMARY KEY模式，当数据库级附加日志模式和表级附加日志模式中有一个为ALL时，该表使用ALL模式。

数据库级附加日志记录FUNCTION，PACKAGE，PROCEDURE，SEQUENCE，TRIGGER，SYNONYM，LIBRARY，TABLE，INDEX，TYPE，VIEW，MATERIALIZED VIEW这些对象的DDL，并且不修改元数据的DDL不会记录。

##### 9.1.1. ALL

ALL模式下，redo里会额外记录DDL文本和DML的rowid，还会在update和delete时记录原行中的所有列（除了LOB，超过32K的varchar和char等）。

这种模式适用于没有主键的表，由于redo里额外记录了整行数据，性能影响较大。

##### 9.1.2. PRIMARY KEY

PRIMARY KEY模式下，redo里会额外记录DDL文本和DML的rowid，还会在update和delete时尝试记录主键列。

如果表没有主键，但有非空的唯一索引，则会记录该索引列。

如果表既没有主键，也没有非空的唯一索引，则会记录原行中的所有列（除了LOB，超过32K的varchar和char等）。

这种模式下，优先记录主键，redo占用较少，性能影响较小。

示例（单机部署）
```sql
--开启最小附加日志
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;

--开启PRIMARY KEY模式的附加日志
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;

--开启ALL模式的附加日志
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

--关闭ALL模式和PRIMARY KEY模式的附加日志
ALTER DATABASE DROP SUPPLEMENTAL LOG DATA (ALL, PRIMARY KEY) COLUMNS;

--关闭最小附加日志
ALTER DATABASE DROP SUPPLEMENTAL LOG DATA;
```

#### 9.2. SUPPLEMENTAL LOG TABLE TYPE

设置数据库级别的DML附加日志对哪些类型的用户表生效。默认为空，在开启附加日志后，请同时设置需要生效的表类型。

LSC表的热数据转冷数据后，所涉及行的rowid可能会发生改变，不建议对LSC表使用rowid作为行的定位信息。
LSC表在导入或通过create table as select创建时，不会产生insert的redo，所以不会记录insert的逻辑日志。

示例（单机部署）
```sql
--设置数据库级附加日志对HEAP和TAC类型的表生效
ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP, TAC);

--设置数据库级附加日志对LSC表不生效
ALTER DATABASE DROP SUPPLEMENTAL LOG TABLE TYPE (LSC);
```

> **Note**: 
>
> 修改数据库级附加日志状态时，不会对正在执行的语句生效。

<span id="flashbackdatabaseclauses" name="flashbackdatabaseclauses" class="yaslink"></span>

### 10. flashback\_database\_clauses

该语句用于配置数据库全库闪回功能。

开启全库闪回后，数据库允许在没有备份集的情况下[闪回](FLASHBACK.html#databaseclauses)到指定时间点。

在高可用部署场景中，全库闪回主要是在主库上开启与执行。对于备库，目前仅适用于特定的故障处理场景，更多详情请查阅[全库闪回](../../全部手册/数据库管理/故障处理/闪回/全库闪回/00全库闪回)。

使用全库闪回功能需遵循如下规则：

- 该语句不适用于存算一体分布式集群部署。

- 需确保数据库已开启归档模式。

- 执行该语句的实例必须处于MOUNT或OPEN状态。

- 在共享集群/分布式集群部署中，全库闪回相关操作只能在主实例（[GV$INSTANCE](../../全部手册/参考手册/系统视图/动态视图/GV$INSTANCE)视图的INSTANCE_ROLE = MASTER_ROLE）上执行。

- 通常，存在[永久还原点](CREATE RESTORE POINT)时无法关闭全库闪回功能，需先手动[删除](DROP RESTORE POINT)所有永久还原点。

- 执行SWITCHOVER时，会强制自动关闭旧主的闪回功能（即使存在永久还原点）。

- 关闭全库闪回功能时，会删除所有普通（非永久）还原点。

> **Note**: 
>
> 开启全库闪回会在一定程度上影响数据库写入性能（估计降低8%左右）。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER DATABASE FLASHBACK ON;
```

<span id="addinstanceclauses" name="addinstanceclauses" class="yaslink"></span>

### 11. add\_instance\_clauses

该语句用于为共享集群新增数据库实例，语句中logfiles_clause、datafiles_clause等的语法及规则同[CREATE DATABASE](CREATE DATABASE)对应子句。

> **Caution**:
>
> 如需进行[共享集群实例扩容](../../全部手册/安装和升级/扩缩容/共享集群扩缩容/集群实例扩缩容)，推荐使用yasboot工具提供的一键式命令，**不建议**单独执行该语句新增数据库实例。

执行本语句需遵循如下规则：

- 仅适用于共享集群/分布式集群部署。
- 只能在主实例（即V$INSTANCE视图中INSTANCE_ROLE字段为MASTER_ROLE的实例）上执行本语句，且主实例需为OPEN状态。若为主备共享集群部署，则只能在主集群的主实例上执行本语句。
- 本语句不能与ALTER DATABASE的add_instance_clauses、降备（Switchover）、表空间等数据文件增/删/RESIZE、Redo文件增/删、YStream创建/运行、分布式事务、备份恢复等操作并发，需等待任一事件完成后才能进行下一个操作。
- 在执行本语句过程中，不允许其他实例从nomount状态变为mount/open状态。
- 单次执行本语句只能添加1个实例，如有需要可重复执行本语句。
- 新增实例后，最终实例总数不能超过当前集群的[MAXINSTANCES](CREATE DATABASE.html#maxinstances)值。

<span id="removeinstanceclauses" name="removeinstanceclauses" class="yaslink"></span>

### 12. remove\_instance\_clauses

该语句用于为共享集群删除数据库实例，语句中instance id可查看[GV$INSTANCE](../../全部手册/参考手册/系统视图/动态视图/GV$INSTANCE)视图。

> **Caution**:
>
> 如需进行[共享集群实例缩容](../../全部手册/安装和升级/扩缩容/共享集群扩缩容/集群实例扩缩容)，推荐使用yasboot工具提供的一键式命令，**不建议**单独执行该语句删除数据库实例。

执行本语句需遵循如下规则：

- 仅适用于共享集群/分布式集群部署。
- 待删除的实例必须处于关闭状态。若为主备共享集群部署，删除备集群的实例时不强制要求其处于关闭状态。
- 只能在主实例（即V$INSTANCE视图中INSTANCE_ROLE字段为MASTER_ROLE的实例）上执行本语句，且主实例需为OPEN状态。若为主备共享集群部署，则只能在主集群的主实例上执行本语句。
- 本语句不能与ALTER DATABASE的add_instance_clauses、降备（Switchover）、表空间等数据文件增/删/RESIZE、Redo文件增/删、ystream创建/运行、分布式事务、备份恢复等操作并发，需等待任一事件完成后才能进行下一个操作。
- 在执行本语句过程中，不允许其他实例加入集群（例如启动至mount/open阶段）。
- 单次执行本语句只能删除1个实例，如有需要可重复执行本语句。
