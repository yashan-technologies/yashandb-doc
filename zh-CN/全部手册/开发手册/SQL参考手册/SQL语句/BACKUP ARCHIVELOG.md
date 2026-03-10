## 通用描述

BACKUP ARCHIVELOG用于执行对当前数据库归档日志文件的备份。

只能在数据库状态为OPEN时备份数据库的归档日志文件，且归档模式必须为开启状态（使用[ALTER DATABASE](./ALTER DATABASE)语句调整归档模式）。

该语句不适用于存算一体分布式集群部署。

通过DBA_ARCHIVE_BACKUPSET视图可以查询生成的备份集信息，通过V$BACKUP_PROGRESS视图可以查询备份的过程信息。

关于备份恢复的详细操作描述请参考[备份与恢复](../../../数据库管理/备份与恢复/00备份与恢复)。

## 语句定义

**backup archivelog::=**

```ebnf+diagram
syntax::= BACKUP ARCHIVELOG archivelogRangeSpecifier [backupCommonSpecifier]
```

**[archive_log_range_specifier](#archivelograngespecifier)::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) 
```

**[backup_common_specifier](#backupcommonspecifier)::=**

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
<span id="archivelograngespecifier" name="archivelograngespecifier" class="yaslink"></span>

### 1. archivelogRangeSpecifier

用于指定需备份的归档日志范围。

若范围区间内有指定的归档日志文件不存在（例如文件已被删除、某一序列号对应的归档日志文件暂未生成等）现象，备份操作会失败并提示错误码YAS-02544。

#### 1.1. SCN

用于指定需备份的归档日志SCN范围，SCN为64位无符号整数。

通过V$ARCHIVED_LOG视图可以获取当前已存在的归档日志文件中第一条日志的SCN（FIRST_CHANGE#字段）、最后一条日志的SCN（NEXT_CHANGE#字段）以及其他详细信息。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG SCN BETWEEN 505411989407080448 AND 505412089999663104 FORMAT 'SCNRANGE' TAG 'SCNRANGE' COMPRESSION;
```

#### 1.2. SEQUENCE

用于指定需备份的归档日志序列号范围，序列号为32位无符号整数。

通过GV$ARCHIVED_LOG/V$ARCHIVED_LOG视图可以获取当前已存在的归档日志文件的序列号（SEQUENCE#字段）以及其他详细信息。

在共享集群/分布式集群部署中，推荐使用SCN或TIME指定归档日志的备份范围。若采用序列号则执行1次`BACKUP ARCHIVELOG SEQUENCE ...`只能备份1个实例（默认为INST_ID=1的实例）上的归档日志文件，必须重复执行该语句并通过THREAD关键字指定不同的实例ID直至完成集群中所有实例上归档日志文件的备份。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 FORMAT 'ASNRANGE' TAG 'ASNRANGE' PARALLELISM 8 SECTION SIZE 128M;
```

#### 1.3. TIME

用于指定需备份的归档日志的时间范围，最早默认不早于当前环境的安装时间，最晚可以设置为未来时间但实际备份的内容截止于最大归档文件序列号所对应文件中的最后一条日志的SCN所对应的时间。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG TIME BETWEEN TO_DATE('2023-10-28 08:14:01','yyyy-mm-dd hh24:mi:ss') AND TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss') FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;
```

#### 1.4. FROM

在归档备份语句中，与SCN、SEQUENCE或TIME配合使用指定归档日志备份的起点。

指定FROM指令备份归档日志时，备份的终点为当前数据库已存在的最大归档文件序列号所对应文件中的最后一条日志，通过V$ARCHIVED_LOG视图可以获取当前已存在的归档日志文件相关信息。

若指定的起点大于（或晚于）终点，则提示错误码YAS-02541，没有符合条件的归档文件。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG FROM SCN 505412089999663104 FORMAT 'FROMSCN' TAG 'FROMSCN' COMPRESSION;

BACKUP ARCHIVELOG FROM SEQUENCE 10 FORMAT 'FROMASN' TAG 'FROMASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG FROM TIME TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'FROMTIME' TAG 'FROMTIME' COMPRESSION;
```

#### 1.5. BETWEEN … AND …

在归档备份语句中，与SCN、SEQUENCE或TIME配合使用指定归档日志备份的区间。

指定BETWEEN …（起点） AND …（终点）指令备份归档日志时，备份的起点和终点均为指定值，通过V$ARCHIVED_LOG视图可以获取当前已存在的归档日志文件相关信息并按需选定起点和终点。

若指定的起点大于（或晚于）终点，则提示错误码YAS-02541，没有符合条件的归档文件。

#### 1.6. UNTIL

在归档备份语句中，与SCN、SEQUENCE或TIME配合使用指定为归档日志备份的终点。

指定UNTIL指令备份归档日志时，备份的起点为当前数据库已存在的最小归档文件序列号所对应文件中的第一条日志，通过V$ARCHIVED_LOG视图可以获取当前已存在的归档日志文件相关信息。

若指定的终点小于（或早于）起点，则提示错误码YAS-02541，没有符合条件的归档文件。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG UNTIL SCN  505412089999663104 FORMAT 'UNTILSCN' TAG 'UNTILSCN' COMPRESSION;

BACKUP ARCHIVELOG UNTIL SEQUENCE 10 FORMAT 'UNTILASN' TAG 'UNTILASN' PARALLELISM 8 SECTION SIZE 128M;

BACKUP ARCHIVELOG UNTIL TIME TO_DATE('2023-11-28 11:14:01','yyyy-mm-dd hh24:mi:ss')  FORMAT 'UNTILTIME' TAG 'UNTILTIME' COMPRESSION;
```
<span id="backupcommonspecifier" name="backupcommonspecifier" class="yaslink"></span>

### 2. backupCommonSpecifier

用于指定备份操作的压缩、加密、并行度等通用配置。

#### 2.1. COMPRESSION

该语句用于压缩备份集，COMPRESSION后的选项都省略时，表示按ZSTD算法和LOW级别压缩备份数据。

**压缩算法**

通过ALGORITHM关键字指定，YashanDB提供如下两种压缩算法供用户选择：

- ZSTD：可提供更高的压缩率。
- LZ4：可提供更高的压缩速率。

**压缩级别**

在指定压缩算法时可同时指定压缩级别，可省略，则缺省采用LOW级别。

压缩级别包含HIGH、MEDIUM、LOW三个选项，其中HIGH表示最高的压缩率（最低的压缩速度），LOW表示最低的压缩率（最高的压缩速度）。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG ALL COMPRESSION;
BACKUP ARCHIVELOG SEQUENCE BETWEEN 2 AND 10 COMPRESSION ALGORITHM lz4 HIGH;
```

#### 2.2. ENCRYPTION

该语句用于加密备份集。

**加密算法**

YashanDB提供如下四种加密算法供选择，不指定时默认采用AES128加密算法：

- AES128
- AES192
- AES256
- SM4

**IDENTIFIED BY**

指定加密备份时必须输入的密钥，该密钥输入的约束规则同用户口令规则，参考[CREATE USER](./CREATE USER)中password描述。

示例（单机/共享集群/分布式集群部署）

```sql
BACKUP ARCHIVELOG ALL ENCRYPTION IDENTIFIED BY 12345;
```

#### 2.3. FORMAT

该语句用于指定生成的备份集的文件名称，省略则默认为`$YASDB_DATA/backup/bak_{date}`。

当文件名称中未指定路径时，默认创建在数据库的$YASDB_DATA/backup文件夹下。

共享集群/分布式集群部署中，通过指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径，可将数据备份至共享存储上；指定为普通磁盘路径时，则将数据备份至实例所在服务器上。

#### 2.4. TAG

该语句用于指定备份集的别名，该别名最大长度为64（包含结束符'\\0'）。

#### 2.5. PARALLELISM

该语句用于指定多线程备份的并行度，取值范围为[1,8]，省略时默认为2。

#### 2.6. SECTION SIZE

该语句用于指定文件分片大小，超过该值的文件会被拆分为多个小文件执行备份，取值范围为[128M,32T]，省略时默认为系统自动计算的最优值。若指定默认文件大小不是1M整数倍，则会向下取整按照1M对齐。

> **Note**: 
>
> 请按照实际数据文件大小选择合适的分片大小，假设当前YashanDB中单个数据文件最大大小为MAXSIZE，建议分片大小应该小于(MAXSIZE/(PARALLELISM*2))，且建议最大不超过4G，其中PARALLELISM为备份指定的并行度，可有效提高备份效率。
