通用描述
----

BACKUP DATABASE用于执行对当前数据库的备份，即将数据库文件拷贝一个副本，以备份集的形式持久化。 

备份可分为全量备份和增量备份：

*   [全量备份](#full)：对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。

*   [增量备份](#incremental)：首次执行基线备份（level 0），后续每次只需备份增量数据（level 1），备份效率高，节省磁盘空间。恢复时需要从基线备份集开始依次恢复所有增量备份。

只能在数据库状态为OPEN且归档模式为开启状态（使用[ALTER DATABASE](./ALTER DATABASE)语句调整归档模式）时，执行该语句备份数据库。

在以下场景中，只能使用YashanDB的配套工具[yasrman](../../../工具手册/yasrman/00yasrman)进行备份与恢复操作：

- 当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，无法使用该语句。如需进行备份恢复，需使用yasrman工具。

- 在存算一体分布式集群部署中，执行该语句只能对操作节点进行备份，无法备份整个分布式集群。如需对分布式集群进行备份，需使用[yasrman](../../../工具手册/yasrman/00yasrman)工具。

通过v$backup_progress视图可以查询备份的过程信息，通过dba_backup_set视图可以查看生成的备份集信息。

关于备份恢复的详细操作描述请参考[备份与恢复](../../../数据库管理/备份与恢复/00备份与恢复)。

> **Note**: 
>
> 如需备份加密对象，需先手动备份密钥钱包文件，恢复前需要先打开对应的密钥钱包文件，再进行恢复操作。

语句定义
----

**backup database::=**

```ebnf
= BACKUP DATABASE ((([FULL] [FORCE] |(INCREMENTAL LEVEL integer [CUMULATIVE|INDEPEND|BASE ON tag_name])) [backupCommonSpecifier])
|(DELETE BACKUPSET [IF EXISTS] (TAG tag_name|PATH backup_path)) | CANCEL).
```

**[backupCommonSpecifier](#backupCommonSpecifier)::=**

```ebnf
= (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
TAG tag_name|
FORMAT backup_path|
SECTION SIZE size_clause)
{" " (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
TAG tag_name|
FORMAT backup_path|
SECTION SIZE size_clause)}.
```

<span id="full" name="full"></span>

### FULL

该语句用于指定备份方式为数据库全量备份，省略是默认为全量备份。

示例（单机/共享集群/分布式集群部署）

```sql
-- 执行全量备份
BACKUP DATABASE FULL;
 
-- 通过视图查看备份信息
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE    INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                       TRUNC_LSN
-------- ------- --------------- ------------- ---------------------------------------------------------------- ----------------------- -----------
       1 FULL                  0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275         187034
```

### FORCE

 

指定是否强制对只读数据库执行全量备份，例如备份主备部署中的备库、异常状态的数据库等。

强制备份不会记录系统表，备份备库时不要求备库连接到主库。

若在数据库处于异常状态时执行备份，则必须指定FORCE关键字。



示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE FULL FORMAT 'backup1' TAG 'backup1' PARALLELISM 8 SECTION SIZE 128M FORCE;
```

<span id="incremental" name="incremental"></span>

### INCREMENTAL

该语句用于指定备份方式为增量备份。

**LEVEL integer**

 

用于指定增量备份级别，不能省略，取值可以为LEVEL 0或LEVEL 1。

- LEVEL 0表示首次增量备份（备份范围等同于全量备份，但在备份概要文件中添加了与全量备份区分的物理标识）。

- LEVEL 1表示非首次增量备份。



执行增量备份时，还可以按需指定INDEPEND|BASE ON tag_name或CUMULATIVE。

#### INDEPEND|BASE ON tag_name

该语句用于指定生成独立增量备份链路的备份集，该类备份集在DBA_BACKUP_SET视图中的DEFAULT_BASE字段显示为FALSE。

- INDEPEND字段仅可在增量备份指定为LEVEL 0时可用。

- BASE ON字段仅在增量备份指定为LEVEL 1时可用，且tag_name指定备份集必须为独立链路的备份集（即在DBA_BACKUP_SET视图中DEFAULT_BASE字段显示为FALSE的备份集）。

仅适用于单机部署。

#### CUMULATIVE

 

对于LEVEL 1增量备份，显式指定CUMULATIVE时表示增量备份方式为累积增量备份，否则为普通增量备份。

*   普通增量备份：普通增量备份的基线（LSN）是上一次增量备份（level 0或level 1均可）。从此种方式的增量备份集进行数据库恢复时，除此增量备份集外还需要至少一个其他的增量备份集。

*   累积增量备份：累积增量备份的基线（LSN）是最近一次level 0的增量备份。从此种方式的增量备份集进行数据库恢复时，除此增量备份集外只需要一个其他的增量备份集。



> **Note**: 
>
> 当备份指定CUMULATIVE后，不可同时指定备份属性为INDEPEND或使用BASE ON语法。

示例（单机/共享集群/分布式集群部署）

```sql
-- 执行首次增量备份（LEVEL 0）
BACKUP DATABASE INCREMENTAL LEVEL 0;

--通过视图查看备份信息
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
									 
-- 执行累积增量备份
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE;

--通过视图查看备份信息
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM dba_backup_set 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
       3 INCREMENTAL                 1             1 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320030305       bak_2023121320030305       187042
```

<span id="backupCommonSpecifier" name="backupCommonSpecifier"></span>

### backupCommonSpecifier

用于指定备份操作的压缩、加密、并行度等通用配置。

#### COMPRESSION



指定备份集的压缩属性，包括压缩算法和压缩级别。若仅指定COMPRESSION关键字（算法与级别均省略）则默认采用ZSTD算法和LOW级别进行压缩。

增量备份的每个备份集允许采用不同的压缩算法及级别，不影响对其的恢复。

**压缩算法**

压缩算法通过ALGORITHM关键字指定，支持以下算法：

- ZSTD：压缩率相对更高。

- LZ4：压缩速率相对更高。

**压缩级别**

压缩级别包括：

- LOW（表示最低的压缩率，相应压缩速度最高）

- MEDIUM

- HIGH



示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION;
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION ALGORITHM lz4 HIGH;
```

#### ENCRYPTION



指定备份集的加密属性，包括加密算法和密钥。若不明确指定加密算法，则默认采用SM4。

增量备份的每个备份集需要保证统一的都加密或都不加密，且每个备份集的密钥必须保持一致。

**加密算法**

支持以下加密算法：

- AES128

- AES192
- AES256
- SM4


**密钥**

加密备份时的密钥通过IDENTIFIED BY关键字指定，密钥的约束规则同[数据库用户的密码规则](./CREATE USER.md#password)。



示例（单机/共享集群/分布式集群部署）

```sql
-- 1. 执行首次增量备份并加密
BACKUP DATABASE INCREMENTAL LEVEL 0 ENCRYPTION IDENTIFIED BY 12345;

-- 后续的增量备份都必须加密
BACKUP DATABASE INCREMENTAL LEVEL 1;
YAS-00607 encryption algorithm error, algorithm name 'UNKNOWN', the baseline backup set is encrypted

-- 后续的增量备份都必须加密且使用相同密钥
BACKUP DATABASE INCREMENTAL LEVEL 1 ENCRYPTION SM4 IDENTIFIED BY 123;
YAS-04261 invalid password: the backup password entered is inconsistent with the baseline backup password

-- 后续的增量备份的加密算法可以不同
BACKUP DATABASE INCREMENTAL LEVEL 1 ENCRYPTION SM4 IDENTIFIED BY 12345;
Succeed.
```

#### PARALLELISM



指定多线程备份的并行度，取值范围为[1,16]，省略时默认为2。



#### TAG



指定备份集的别名，该别名最大长度为64（包含结束符'\\0'）。



#### FORMAT



指定备份集文件名称（可包含路径），省略则默认为`$YASDB_DATA/backup/bak_{date}`。若指定时未包含路径，默认创建在$YASDB_DATA/backup文件夹下。

在共享集群/分布式集群部署中，可指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径将备份集保存至共享存储，若指定为本地路径则将备份集保存至实例所在服务器上。

#### SECTION SIZE



指定文件的分片规格，超过该值的文件会被拆分为多个小文件执行备份，取值范围为[128M,32T]，省略时默认为系统自动计算的最优值。

配置建议如下：

- 建议结合实际的数据文件大小指定分片规格。

  假设当前YashanDB中单个数据文件最大大小为MAXSIZE，建议分片规格小于`MAXSIZE/(PARALLELISM*2)`。

- 建议分片规格小于4G。

若指定的数值不是1M的整数倍，会向下取整按1M对齐。



示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE FULL FORMAT 'backup' TAG 'backup' PARALLELISM 8 SECTION SIZE 128M;
```

### DELETE BACKUPSET

该语句用于删除备份集以及DBA_BACKUP_SET里的备份集记录。

#### IF EXISTS

备份集删除命令可选项。若不确定指定备份集TAG的视图信息或者备份集物理文件是否存在时可指定该选项，均返回删除成功。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE DELETE BACKUPSET TAG 'backup';
BACKUP DATABASE DELETE BACKUPSET PATH '/data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275';
BACKUP DATABASE DELETE BACKUPSET IF EXISTS TAG 'backup';
```

> **Note**: 
>
> 执行指定备份集TAG的备份集删除命令将删除备份视图内容和物理备份集。指定物理备份集目录则只删除物理备份集，该备份集对应的视图内容仍然保留。
>
> 若备份视图残留、备份集物理文件已被删除，可指定IF EXISTS选项执行备份集删除命令，即可删除成功且返回删除成功结果。

#### TAG

用于指定待删除的备份集，以别名确认备份集目标，可查询DBA_BACKUP_SET视图获取TAG信息。

#### PATH

用于指定待删除的备份集，以备份集文件路径确认备份集目标，可查询DBA_BACKUP_SET视图获取PATH信息。

### CANCEL

该语句用于取消当前的backup、restore或build任务。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE CANCEL;
```
