BACKUP可以备份主数据库/备数据库、表空间、归档文件，也部分支持共享集群和分布式集群的备份功能。

在单机部署和共享集群部署中，执行BACKUP操作时系统会先自动进行备份环境预检查，检查项目包括备份路径是否可达、目标地址空余磁盘空间是否充足。检查通过再进行备份，检查失败则直接退出。

## 通用备份属性

<span id="backupCommonSpecifier" name="backupCommonSpecifier" class="yaslink"></span>
**backupCommonSpecifier::=**

```ebnf+diagram
syntax::= ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|
PARALLELISM integer|
SECTION SIZE size_clause|
SKIP VALIDATE|
DEST (server|client)|
PARAMS xbsa_parameter)
{" " ((COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]])|
(ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password)|
(FORMAT backup_path)|
PARALLELISM integer|
SECTION SIZE size_clause|
SKIP VALIDATE|
DEST (server|client)|
PARAMS xbsa_parameter)}
```

### COMPRESSION

压缩属性，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

### ENCRYPTION

加密属性，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

### FORCE

是否强制备份，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

### FORMAT

备份集文件名称（含路径），规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

当format未指定路径时，采用如下默认路径：

- 备份在服务端（DEST SERVER）的备份集默认存储在$YASDB_DATA/backup文件夹下。在共享集群部署中，还可以指定为YFS路径将备份集文件存放至共享存储。

- 备份在工具端（DEST CLIENT）的备份集默认存储在catalog/backup文件夹下。

> **Note**: 
>
> 分布式集群中，当多个节点部署在同一台服务器时，FORMAT建议使用相对路径，否则可能出现路径冲突而导致备份失败。

### PARALLELISM

并行度，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

### SECTION SIZE

文件分片规格，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

> **Note**: 
>
> 请按照实际数据文件大小选择合适的分片大小，假设当前YashanDB中单个数据文件最大大小为MAXSIZE，建议分片大小应该小于(MAXSIZE/(PARALLELISM*2))，且建议最大不超过4G，其中PARALLELISM为备份指定的并行度，可有效提高备份效率。

### DEST

指定备份集的存储位置，可省略，默认为SERVER。

- CLIENT表示存储到工具端，即备份集保存在yasrman所在服务器。

- SERVER表示备份到数据库服务器端，即备份集保存在数据库所在服务器（共享集群中可通过FORMAT指定YFS路径将备份集文件存放至共享存储）。

在存算一体分布式集群部署中，只能使用SERVER。

执行增量备份时，同一基线的备份命令的DEST选项必须与基线保持一致，即同一组增量备份集要么都在工具端，要么都在服务器端，否则恢复时可能找不到基线备份集而报错。

### PARAMS

该语句用于对接第三方备份软件，指定XBSA接口的配置参数进行远程流式备份。

使用该语句时，必须指定备份集的存储位置为工具端（DEST CLIENT），而FORMAT参数将被忽略。

存算一体分布式集群部署中无法使用该语句。

格式如下：
PARAMS 'XBSA_LIBRARY=xbsa_library_path, TOKEN=token_value, ENV=(key1=val1,key2=val2,...)'

- xbsa_library_path：XBSA动态链接库的绝对路径，目前仅支持Linux的动态链接库。
- token_value：BSAInit接口的token入参。
- keyX, valX: BSAInit接口env入参，所有key-value对的最大总长度要小于4096。

示例（单机、共享集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_full_1' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234561)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_0' incremental level 0 compression algorithm lz4 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_1' incremental level 1 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all2' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog
```

## 全库备份

```ebnf+diagram
syntax::= BACKUP (CLUSTER|DATABASE) TAG tag_name 
((FULL|(INCREMENTAL LEVEL integer [CUMULATIVE]))|backupCommonSpecifier)
{ " " ((FULL|(INCREMENTAL LEVEL integer [CUMULATIVE]))|backupCommonSpecifier)} [FORCE]
```

### CLUSTER|DATABASE

用于指定不同部署形态的数据库备份。

- 使用DATABASE关键字表示对单机或共享集群数据库执行备份。

- 使用CLUSTER关键字用于存算一体分布式集群部署的数据库备份，表示对分布式集群中的所有CN节点、DN主节点和MN主节点发起备份。执行过程中任何一个节点备份出现异常时，所有节点备份都将失败，本命令抛出错误。

### FULL

全量备份，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE.html#full)语句中相关描述。

### INCREMENTAL LEVEL

增量备份，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE.html#incremental)语句中相关描述。

### TAG

备份集的唯一标识，用于区分备份集，规则与定义同[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)语句中相关描述。

### backupCommonSpecifier

压缩、加密等通用备份属性，请查阅[通用备份属性](#backupCommonSpecifier)。

示例（单机、共享集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 0 compression encryption aes128 identified by 123456 format 'incr_base' tag 'incr_base' dest server;" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 1 compression algorithm lz4 encryption aes128 identified by 123456 format 'incr_1' tag 'incr_1' dest server;" -D /home/yashan/catalog
```

> **Note**: 
>
> 共享集群部署中的yasrman备份恢复命令与单机部署中完全一致，且备份的目标节点可以指定任一存活节点。

示例（存算一体分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 0 compression format 'cluster_incr_base' tag 'cluster_incr_base';" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 1 compression algorithm zstd format 'cluster_incr_1' tag 'cluster_incr_1';" -D /home/yashan/catalog
```

## 表空间备份

```ebnf+diagram
syntax::= BACKUP TABLESPACE ((TBS_NAME) {"," (TBS_NAME)}) TAG tag_name 
(backupCommonSpecifier)
{ " " (backupCommonSpecifier)}
```

表空间备份的使用约束如下：

- 仅适用于单机部署中的主数据库。

- 无法备份临时表空间、UNDO表空间和损坏的表空间。

- 备份操作与文件增删、表空间offline、online等操作互斥。

- 表空间备份不包含归档文件，且不备份BUCKET文件。

- 表空间文件恢复失败后，该表空间文件不可执行备份操作。

### TABLESPACE

使用本关键字用于指定表空间备份，即备份指定的数据库中的一个或多个表空间下的所有数据文件。

TBS_NAME表示需要手动指定的表空间名，且该表空间必须存在于目标数据库中。

### backupCommonSpecifier

按需指定备份属性。

示例（单机部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user tag 'user_bak'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user,system tag 'sys_bak'" -D /home/yashan/catalog
```

## 归档日志备份

```ebnf+diagram
syntax::= BACKUP ARCHIVELOG archivelogRangeSpecifier (TAG tag_name [backupCommonSpecifier])
```

**archivelogRangeSpecifier::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) [NOT BACKED UP integer TIMES]
```

归档日志备份不适用于存算一体分布式集群部署。

归档日志备份前，必须确保所有节点上的数据库为OPEN状态，且已开启归档。

### ARCHIVELOG

使用本关键字表示对目标数据库的归档日志文件执行备份。

### SEQUENCE|SCN|TIME

基于SEQUENCE、SCN或时间指定需要备份的归档日志范围，规则与定义同[BACKUP ARCHIVELOG](../../../开发手册/SQL参考手册/SQL语句/BACKUP ARCHIVELOG)语句中相关描述。

> **Note**: 
>
> - 若指定时间，时间格式必须保证为yyyy-mm-dd hh24:mi:ss格式。
>
> - 若指定的SEQUENCE范围内没有归档文件，则会提示错误码YAS-02544。

### FROM|BETWEEN … AND …|UNTIL

配合SEQUENCE、SCN或时间指定备份起/止点，规则与定义同[BACKUP ARCHIVELOG](../../../开发手册/SQL参考手册/SQL语句/BACKUP ARCHIVELOG)语句中相关描述。

### NOT BACKED UP integer TIMES

指定备份归档时过滤（不进行备份）已备份达到integer及以上次数的归档文件。

> **Note**: 
>
> 若因指定过滤备份次数而导致最终的备份区间归档不连续，会报错提示并中断此次备份任务。

```shell
# 备份全部归档日志，并跳过已经备份过两次的归档文件
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all' not backed up 2 times" -D /home/yashan/catalog
```

### backupCommonSpecifier

压缩、加密等通用备份属性，请查阅[通用备份属性](#backupCommonSpecifier)。

示例（单机、共享集群部署）

### SKIP VALIDATE

存算一体分布式集群部署模式默认不检查，其他部署模式默认检查。

指定该关键字用于跳过备份预检查。

预检查项包括备份集存储路径是否可达、磁盘剩余空间等，通过预检查可有效避免因磁盘空间等问题导致备份失败，提高备份效率节约时间。

```shell
# 备份全部归档日志
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all'" -D /home/yashan/catalog

# 基于sequence选择备份范围（10 ~ 20）
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog sequence between 10 and 20 tag 'sequence_10_20' format 'sequence_10_20'" -D /home/yashan/catalog

# 基于时间选择备份范围并进行压缩备份
$ yasrman sys/********@192.168.1.2:1688 -c "BACKUP ARCHIVELOG TIME BETWEEN '2023-10-28 08:14:01' AND '2023-11-28 11:14:01'  FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;" -D /home/yashan/catalog
```
