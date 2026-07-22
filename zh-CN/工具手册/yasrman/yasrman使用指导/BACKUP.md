BACKUP可以备份主数据库/备数据库、表空间、归档文件，也部分支持共享集群和分布式集群的备份功能。

在单机/共享集群/分布式集群部署中，执行BACKUP操作时系统会先自动进行备份环境预检查，检查项目包括备份路径是否可达、目标地址空余磁盘空间是否充足。检查通过再进行备份，检查失败则直接退出。

<span id="backupCommonSpecifier" name="backupCommonSpecifier"></span>

## 通用备份属性

**backupCommonSpecifier::=**

```ebnf
= (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
DEST (SERVER|CLIENT)|
(FORMAT backup_path)|
SECTION SIZE size_clause|
SKIP VALIDATE|
PARAMS xbsa_parameter)
{" " (COMPRESSION [ALGORITHM (ZSTD|LZ4) [LOW|MEDIUM|HIGH]]|
ENCRYPTION [AES128|AES192|AES256|SM4] IDENTIFIED BY password|
PARALLELISM integer|
DEST (SERVER|CLIENT)|
(FORMAT backup_path)|
SECTION SIZE size_clause|
SKIP VALIDATE|
PARAMS xbsa_parameter)}.
```

### COMPRESSION



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



### ENCRYPTION



指定备份集的加密属性，包括加密算法和密钥。若不明确指定加密算法，则默认采用SM4。

增量备份的每个备份集需要保证统一的都加密或都不加密，且每个备份集的密钥必须保持一致。

**加密算法**

支持以下加密算法：

- AES128

- AES192
- AES256
- SM4


**密钥**

加密备份时的密钥通过IDENTIFIED BY关键字指定，密钥的约束规则同[数据库用户的密码规则](../../../开发手册/SQL参考手册/SQL语句/CREATE USER.md#password)。



### PARALLELISM



指定多线程备份的并行度，取值范围为[1,16]，省略时默认为2。



### DEST

指定备份目的端，即备份集的存储位置，可省略，默认为SERVER。

- SERVER：表示备份至数据库服务器端，即备份集保存在数据库所在服务器（共享集群/分布式集群部署中可通过FORMAT指定YFS路径将备份集文件存放至共享存储）。

- CLIENT：表示备份至工具端，即备份集保存在yasrman所在服务器。

在存算一体分布式集群部署中，只能使用SERVER。

执行增量备份时，同一基线的备份命令的DEST选项必须与基线保持一致，即同一组增量备份集必须放在同一台设备上，否则恢复时可能因找不到基线备份集而报错。

### FORMAT

指定备份集文件名称（可包含路径），省略时文件名默认为`bak_{date}`，默认存储路径则取决于备份目的端：

- 备份至服务端（DEST SERVER）：备份集默认存储在$YASDB_DATA/backup文件夹下。

- 备份至工具端（DEST CLIENT）：备份集默认存储在catalog/backup文件夹下。

在共享集群/分布式集群部署中，可指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径将备份集保存至共享存储，若指定为本地路径则将备份集保存至实例所在服务器上。

> **Note**: 
>
> 在存算一体分布式集群部署中，当多个节点部署在同一台服务器时，FORMAT建议使用相对路径，否则可能出现路径冲突而导致备份失败。

### SECTION SIZE



指定文件的分片规格，超过该值的文件会被拆分为多个小文件执行备份，取值范围为[128M,32T]，省略时默认为系统自动计算的最优值。

配置建议如下：

- 建议结合实际的数据文件大小指定分片规格。

  假设当前YashanDB中单个数据文件最大大小为MAXSIZE，建议分片规格小于`MAXSIZE/(PARALLELISM*2)`。

- 建议分片规格小于4G。

若指定的数值不是1M的整数倍，会向下取整按1M对齐。



### SKIP VALIDATE

指定是否跳过备份预检查，省略时仅存算一体分布式集群部署中默认不检查，其他均默认检查。

预检查项包括备份集存储路径是否可达、磁盘剩余空间等，通过预检查可有效避免因磁盘空间等问题导致备份失败，提高备份效率节约时间。

### PARAMS

该语句用于对接第三方备份软件，指定XBSA接口的配置参数进行远程流式备份。

使用该语句时，必须指定备份集的存储位置为工具端（DEST CLIENT），而FORMAT参数将被忽略。

存算一体分布式集群部署中无法使用该语句。

格式如下：
PARAMS 'XBSA_LIBRARY=xbsa_library_path, TOKEN=token_value, ENV=(key1=val1,key2=val2,...)'

- xbsa_library_path：XBSA动态链接库的绝对路径，目前仅支持Linux的动态链接库。
- token_value：BSAInit接口的token入参。
- keyX, valX: BSAInit接口env入参，所有key-value对的最大总长度要小于4096。

示例（单机、共享集群、分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_full_1' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234561)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_0' incremental level 0 compression algorithm lz4 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'xbsa_incr_1' incremental level 1 dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all2' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234563)'" -D /home/yashan/catalog
```

## 全库备份

```ebnf
= BACKUP dbObjectSpecifier TAG tag_name 
([FULL]|(INCREMENTAL LEVEL integer [CUMULATIVE])) 
[backupCommonSpecifier] 
[FORCE].
```

**dbObjectSpecifier::=**

```ebnf
= DATABASE [ROOT]|PLUGGABLE DATABASE (pdb_name {"," pdb_name}) |CLUSTER.
```

### dbObjectSpecifier

通过关键字指定备份对象：

| 适用场景 | 关键字 | 功能描述 |
|-----------------|-------------|-------------------------|
| 非容器数据库 | DATABASE  | 用于备份单机部署或共享集群部署的整个数据库。 |
| 非容器数据库 | CLUSTER | 用于备份存算一体分布式集群部署的整个数据库，即对存算一体分布式集群中的所有CN节点、DN主节点和MN主节点发起备份。若执行过程中任何1个节点的备份出现异常，所有节点备份都将失败，本命令抛出错误。 |
| 容器数据库 | DATABASE | 用于备份整个数据库或单个PDB，具体取决于使用yasrman所连接登录的对象：<br/>* 连接根容器时：备份根容器以及所有PDB。<br/>* 连接某个PDB时：备份该PDB。 |
| 容器数据库 | DATABASE ROOT | 用于备份根容器，必须连接根容器执行。  |
| 容器数据库 | PLUGGABLE DATABASE | 用于备份指定PDB或种子容器，必须采用具备指定PDB的备份和连接权限的用户连接根容器执行。 |

### FULL

指定备份方式为全量备份，省略是默认为全量备份。

全量备份是指对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。

### INCREMENTAL

指定备份方式为增量备份。

采用增量备份时，备份效率更高，更节省磁盘空间。恢复时需要从基线备份集开始依次恢复所有增量备份。

#### LEVEL integer

 

用于指定增量备份级别，不能省略，取值可以为LEVEL 0或LEVEL 1。

- LEVEL 0表示首次增量备份（备份范围等同于全量备份，但在备份概要文件中添加了与全量备份区分的物理标识）。

- LEVEL 1表示非首次增量备份。

 

#### CUMULATIVE

 

对于LEVEL 1增量备份，显式指定CUMULATIVE时表示增量备份方式为累积增量备份，否则为普通增量备份。

*   普通增量备份：普通增量备份的基线（LSN）是上一次增量备份（level 0或level 1均可）。从此种方式的增量备份集进行数据库恢复时，除此增量备份集外还需要至少一个其他的增量备份集。

*   累积增量备份：累积增量备份的基线（LSN）是最近一次level 0的增量备份。从此种方式的增量备份集进行数据库恢复时，除此增量备份集外只需要一个其他的增量备份集。

 

### TAG



指定备份集的唯一标识，用于区分备份集，标识名最大长度为64（包含结束符'\\0'）。



### backupCommonSpecifier

压缩、加密等通用备份属性，请查阅[通用备份属性](#backupCommonSpecifier)。

示例（单机、共享集群、分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 0 compression encryption aes128 identified by 123456 format 'incr_base' tag 'incr_base' dest server;" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 1 compression algorithm lz4 encryption aes128 identified by 123456 format 'incr_1' tag 'incr_1' dest server;" -D /home/yashan/catalog
```

示例（容器数据库）

```shell
# 连接根容器，备份全库
$ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'cdb_full' format 'cdb_full'" -D /home/yashan/catalog

# 连接根容器，备份指定PDB
$ yasrman sys/********@192.168.1.2:1688 -c "backup pluggable database pdb1,pdb2 format 'part_pdb' tag 'part_pdb' dest server;" -D /home/yashan/catalog
```

> **Note**: 
>
> 共享集群/分布式集群部署中的yasrman备份恢复命令与单机部署中完全一致，且备份的目标节点可以指定任一存活节点。

示例（存算一体分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 0 compression format 'cluster_incr_base' tag 'cluster_incr_base';" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup cluster incremental level 1 compression algorithm zstd format 'cluster_incr_1' tag 'cluster_incr_1';" -D /home/yashan/catalog
```

### FORCE

 

指定是否强制对只读数据库执行全量备份，例如备份主备部署中的备库、异常状态的数据库等。

强制备份不会记录系统表，备份备库时不要求备库连接到主库。

若在数据库处于异常状态时执行备份，则必须指定FORCE关键字。

 

## 表空间备份

```ebnf
= BACKUP TABLESPACE (TBS_NAME {"," TBS_NAME}) TAG tag_name 
(backupCommonSpecifier)
{ " " (backupCommonSpecifier)}.
```

表空间备份的使用约束如下：

- 仅适用于单机部署，非容器数据库需连接主库执行操作，容器数据库中则需直连主PDB。

- 需直连目标表空间所属数据库/PDB执行备份操作。

- 无法备份临时表空间、UNDO表空间和损坏的表空间。

- 备份操作与文件增删、表空间offline、online等操作互斥。

- 表空间备份不包含归档文件，且不备份BUCKET文件。

- 表空间文件恢复失败后，该表空间文件不可执行备份操作。

### TABLESPACE

使用本关键字用于指定表空间备份，即备份指定的数据库中的一个或多个表空间下的所有数据文件。

TBS_NAME表示需要手动指定的表空间名，且该表空间必须存在于目标数据库中。

### TAG



指定备份集的唯一标识，用于区分备份集，标识名最大长度为64（包含结束符'\\0'）。



### backupCommonSpecifier

按需指定备份属性。

示例（单机部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user tag 'user_bak'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace user,system tag 'sys_bak'" -D /home/yashan/catalog
```

## 归档日志备份

```ebnf
= BACKUP archivelogObjectSpecifier archivelogRangeSpecifier (TAG tag_name [backupCommonSpecifier]).
```

**archivelogObjectSpecifier::=**

```ebnf
= ARCHIVELOG [ROOT]| PLUGGABLE ARCHIVELOG (pdb_name {"," pdb_name}).
```

**archivelogRangeSpecifier::=**

```ebnf
= ((
(FROM SCN | SCN BETWEEN integer AND | UNTIL SCN ) integer | 
(FROM SEQUENCE | SEQUENCE BETWEEN integer AND | UNTIL SEQUENCE ) integer [THREAD integer] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL)) [NOT BACKED UP integer TIMES].
```

归档日志备份不适用于存算一体分布式集群部署。

归档日志备份前，必须确保所有节点上的数据库为OPEN状态，且已开启归档。

### archivelogObjectSpecifier

通过关键字指定备份对象：

| 适用场景 | 关键字 | 功能描述 |
|--------------------|-------------|-------------------------|
| 非容器数据库 | ARCHIVELOG  | 用于备份单机部署或共享集群部署的归档日志文件。 |
| 容器数据库 | ARCHIVELOG | 用于备份整个数据库或单个PDB的归档日志文件，具体取决于使用yasrman所连接登录的对象：<br/>* 连接根容器时：备份根容器、种子容器以及所有PDB的归档日志文件，但仅支持通过ALL、UNTIL TIME或UNTIL SCN指定备份区间。<br/>* 连接某个PDB时：备份该PDB的归档日志文件。 |
| 容器数据库 | ARCHIVELOG ROOT | 用于备份根容器的归档日志文件，必须连接根容器执行。<br/>仅支持使用ALL、UNTIL TIME或UNTIL SCN指定备份区间。  |
| 容器数据库 | PLUGGABLE ARCHIVELOG | 用于备份指定PDB的归档日志文件，必须采用具备指定PDB的备份和连接权限的用户连接根容器执行。<br/>仅支持通过ALL、UNTIL TIME或UNTIL SCN指定备份区间。 |

### SEQUENCE|SCN|TIME

基于SEQUENCE、SCN或时间指定需要备份的归档日志范围，规则与定义同[BACKUP ARCHIVELOG](../../../开发手册/SQL参考手册/SQL语句/BACKUP ARCHIVELOG)语句中相关描述。

> **Note**: 
>
> - 若指定时间，时间格式必须保证为yyyy-mm-dd hh24:mi:ss格式。
>
> - 若指定的SEQUENCE范围内没有归档文件，则会提示错误码YAS-02544。
>
> - 在容器数据库中，直连根容器时仅支持指定ALL、UNTIL TIME或UNTIL SCN指定归档日志范围，直连PDB时不受限制。


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

### TAG



指定备份集的唯一标识，用于区分备份集，标识名最大长度为64（包含结束符'\\0'）。



### backupCommonSpecifier

压缩、加密等通用备份属性，请查阅[通用备份属性](#backupCommonSpecifier)。

示例（单机、共享集群、分布式集群部署）

```shell
# 备份全部归档日志
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog all tag 'arch_all' format 'arch_all'" -D /home/yashan/catalog

# 基于sequence选择备份范围（10 ~ 20）
$ yasrman sys/********@192.168.1.2:1688 -c "backup archivelog sequence between 10 and 20 tag 'sequence_10_20' format 'sequence_10_20'" -D /home/yashan/catalog

# 基于时间选择备份范围并进行压缩备份
$ yasrman sys/********@192.168.1.2:1688 -c "BACKUP ARCHIVELOG TIME BETWEEN '2025-10-28 08:14:01' AND '2025-11-28 11:14:01'  FORMAT 'TIMERANGE' TAG 'TIMERANGE' COMPRESSION;" -D /home/yashan/catalog
```
