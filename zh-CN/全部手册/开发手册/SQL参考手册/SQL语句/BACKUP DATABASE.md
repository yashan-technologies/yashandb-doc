通用描述
----

BACKUP DATABASE用于执行对当前数据库的备份，即将数据库文件拷贝一个副本，以备份集的形式持久化。 

备份可分为全量备份和增量备份：

*   [全量备份](#full)：对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。

*   [增量备份](#incremental)：首次执行基线备份（level 0），后续每次只需备份增量数据（level 1），备份效率高，节省磁盘空间。恢复时需要从基线备份集开始依次恢复所有增量备份。

只能在数据库状态为OPEN时备份数据库，且归档模式必须为开启状态（使用[ALTER DATABASE](./ALTER DATABASE)语句调整归档模式）。

存算一体分布式集群部署中，用户可以执行此语句，但只能对所在节点进行备份，因此不建议使用。如需对分布式集群进行备份，需使用[yasrman](../../../工具手册/yasrman/00yasrman)工具。

通过V$BACKUP_PROGRESS视图可以查询备份的过程信息，通过DBA_BACKUP_SET视图可以查看生成的备份集信息。

关于备份恢复的详细操作描述请参考[备份与恢复](../../../数据库管理/备份与恢复/00备份与恢复)。

> **Note**: 
>
> 如需备份加密对象，需先手动备份密钥钱包文件，恢复前需要先打开对应的密钥钱包文件，再进行恢复操作。

语句定义
----

**backup database::=**

```ebnf+diagram
syntax::= BACKUP DATABASE (((FULL|(INCREMENTAL LEVEL integer [CUMULATIVE|INDEPEND|BASE ON tag_name])) [backupCommonSpecifier][FORCE])
|(DELETE BACKUPSET [IF EXISTS] (TAG tag_name|PATH backup_path)) | CANCEL)
```

**[backup_common_specifier](#backupCommonSpecifier)::=**

```ebnf+diagram
syntax::= ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|(TAG tag_name)|
PARALLELISM integer|
SECTION SIZE size_clause)
{" " ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|(TAG tag_name)|
PARALLELISM integer|
SECTION SIZE size_clause)}
```

<span id="full" name="full" class="yaslink"></span>

### 1. FULL

该语句用于指定备份方式为数据库全量备份，全量备份是默认的备份方式。

示例（单机/共享集群/分布式集群部署）

```sql
-- 执行全量备份
BACKUP DATABASE FULL;
 
-- 通过视图查看备份信息
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM DBA_BACKUP_SET 
ORDER BY START_TIME;
  RECID# TYPE    INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                       TRUNC_LSN
-------- ------- --------------- ------------- ---------------------------------------------------------------- ----------------------- -----------
       1 FULL                  0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275         187034
```

<span id="incremental" name="incremental" class="yaslink"></span>

### 2. INCREMENTAL

该语句用于指定备份方式为增量备份。

**LEVEL integer**

用于指定增量备份级别，不能省略，取值可以为LEVEL 0或LEVEL 1。

- LEVEL 0表示首次增量备份（备份范围等同于全量备份，但在备份概要文件中添加了与全量备份区分的物理标识）。

- LEVEL 1表示非首次增量备份。

执行增量备份时，还可以按需指定INDEPEND|BASE ON tag_name或CUMULATIVE。

#### 2.1. INDEPEND|BASE ON tag\_name

该语句用于指定生成独立增量备份链路的备份集，该类备份集在DBA_BACKUP_SET视图中的DEFAULT_BASE字段显示为FALSE。

- INDEPEND字段仅可在增量备份指定为LEVEL 0时可用。

- BASE ON字段仅在增量备份指定为LEVEL 1时可用，且tag_name指定备份集必须为独立链路的备份集（即在DBA_BACKUP_SET视图中DEFAULT_BASE字段显示为FALSE的备份集）。

仅适用于单机部署。

#### 2.2. CUMULATIVE

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
FROM DBA_BACKUP_SET 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
									 
-- 执行累积增量备份
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE;

--通过视图查看备份信息
SELECT RECID#,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN 
FROM DBA_BACKUP_SET 
ORDER BY START_TIME;
  RECID# TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                     TRUNC_LSN
-------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------- ----------
       1 FULL                        0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121319584275       bak_2023121319584275       187034
       2 INCREMENTAL                 0             0 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320010894       bak_2023121320010894       187038
       3 INCREMENTAL                 1             1 /data/yashan/yasdb_data/db-1-1/backup/bak_2023121320030305       bak_2023121320030305       187042
```

<span id="backupCommonSpecifier" name="backupCommonSpecifier" class="yaslink"></span>

### 3. backupCommonSpecifier

#### 3.1. COMPRESSION

该语句用于压缩备份集，COMPRESSION后的选项都省略时，表示按ZSTD算法和LOW级别压缩备份数据。

增量备份的每个备份集可采用不同的压缩算法及级别，不影响对其的恢复。

**压缩算法**

通过ALGORITHM关键字指定，支持ZSTD和LZ4。

- ZSTD：提供更高的压缩率

- LZ4：提供更高的压缩速率。

**压缩级别**

在指定压缩算法时可同时指定压缩级别，可省略，则缺省采用LOW级别。

压缩级别包含HIGH、MEDIUM、LOW，HIGH表示最高的压缩率（最低的压缩速度），LOW表示最低的压缩率（最高的压缩速度）。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION;
BACKUP DATABASE INCREMENTAL LEVEL 1 CUMULATIVE COMPRESSION ALGORITHM lz4 HIGH;
```

#### 3.2. ENCRYPTION

该语句用于加密备份集。

增量备份的每个备份集需要保证统一的都加密或者都不加密，且每个备份集的密钥必须保持一致。

**加密算法**

YashanDB提供如下四种加密算法供选择，不指定时默认采用SM4加密算法：

- AES128
- AES192
- AES256
- SM4

**IDENTIFIED BY**

指定加密备份时必须输入的密钥，该密钥输入的约束规则同用户口令规则，参考[CREATE USER](./CREATE USER)中password描述。

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
```

#### 3.3. FORMAT

该语句用于指定生成的备份集的文件名称，省略则默认为`$YASDB_DATA/backup/bak_{date}`。

当文件名称中未指定路径时，默认创建在数据库的$YASDB_DATA/backup文件夹下。

共享集群/分布式集群部署中，通过指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径，可将数据备份至共享存储上；指定为普通磁盘路径时，则将数据备份至实例所在服务器上。

#### 3.4. TAG

该语句用于指定备份集的别名，该别名最大长度为64（包含结束符'\\0'）。

#### 3.5. PARALLELISM

该语句用于指定多线程备份的并行度，取值范围为[1,16]，省略时默认为2。

#### 3.6. SECTION SIZE

该语句用于指定文件分片大小，超过该值的文件会被拆分为多个小文件执行备份，取值范围为[128M,32T]，省略时默认为系统自动计算的最优值。若指定默认文件大小不是1M整数倍，则会向下取整按照1M对齐。

> **Note**: 
>
> 请按照实际数据文件大小选择合适的分片大小，假设当前YashanDB中单个数据文件最大大小为MAXSIZE，建议分片大小应该小于(MAXSIZE/(PARALLELISM*2))，且建议最大不超过4G，其中PARALLELISM为备份指定的并行度，可有效提高备份效率。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE FULL FORMAT 'backup' TAG 'backup' PARALLELISM 8 SECTION SIZE 128M;
```

### 4. FORCE

该语句用于指定是否强制备份，仅用于全量备份。

强制备份被应用于对只读数据库的备份，例如主备形态中的备库备份，强制备份不会记录系统表，也不需要备库连接到主库。

若在数据库处于异常状态时执行备份，则必须指定FORCE关键字。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE FULL FORMAT 'backup1' TAG 'backup1' PARALLELISM 8 SECTION SIZE 128M FORCE;
```

### 5. DELETE BACKUPSET

该语句用于删除备份集以及DBA_BACKUP_SET里的备份集记录。

#### 5.1. IF EXISTS

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

#### 5.2. TAG

用于指定待删除的备份集，以别名确认备份集目标，可查询DBA_BACKUP_SET视图获取TAG信息。

#### 5.3. PATH

用于指定待删除的备份集，以备份集文件路径确认备份集目标，可查询DBA_BACKUP_SET视图获取PATH信息。

### 6. CANCEL

该语句用于取消当前的backup、restore或build任务。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP DATABASE CANCEL;
```
