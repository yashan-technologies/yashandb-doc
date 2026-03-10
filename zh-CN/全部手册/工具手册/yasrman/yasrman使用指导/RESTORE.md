使用RESTORE命令可基于yasrman生成的备份集进行恢复，包括全库恢复、某一表空间、归档文件的恢复操作。

在单机/共享集群/分布式集群部署中，执行RESTORE操作时系统会先自动进行环境预检查，检查项目包括待恢复文件路径是否可达、目标地址空余磁盘空间是否充足。检查通过再进行RESTORE，检查失败则直接退出。

## 通用恢复属性

<span id="restoreCommonSpecifier" name="restoreCommonSpecifier" class="yaslink"></span>
**restoreCommonSpecifier::=**

```ebnf+diagram
syntax::= ((DECRYPTION password)|
(PARALLELISM integer)|
(PARAMS xbsa_parameter)|(DEST (SERVER|CLIENT))) 
{ " " ((DECRYPTION password)|
(PARALLELISM integer)|
(PARAMS xbsa_parameter)|(DEST (SERVER|CLIENT)))}
```

### DECRYPTION

该语句用于指定备份集恢复时解密，此时需要同时指定解密密码。

### PARALLELISM

该语句用于指定多线程恢复的并行度，该值范围为[1,16]，省略时默认为2。

### PARAMS

该语句用于进行流式备份集的恢复，指定XBSA接口的配置参数。使用该语句时，必须指定备份集的存储位置为工具端（DEST CLIENT）。

存算一体分布式集群部署中无法使用该语句。

格式如下：
'XBSA_LIBRARY=xbsa_library_path, TOKEN=token_value, ENV=(key1=val1,key2=val2,...)'

- xbsa_library_path：XBSA动态链接库的绝对路径，目前仅支持Linux的动态链接库。
- token_value：BSAInit接口的token入参。
- keyX, valX: BSAInit接口env入参，所有key-value对的最大总长度要小于4096。

示例（单机、共享集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'xbsa_full_1' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234561)'" -D /home/yashan/catalog
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'xbsa_incr_0' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "restore archivelog all from tag 'arch_all2' dest client params 'XBSA_LIBRARY=/home/yashan/lib/libxbsa.so, TOKEN=157257815837, ENV=(BSA_SERVICE_HOST=192.168.1.1:1601,BSA_BACKUP_UUID=yashan1234562)'" -D /home/yashan/catalog
```

### DEST

仅做语法兼容，但建议与备份时指定的DEST保持一致，实际恢复时将按照CATALOG记录位置灵活恢复。

## 全库恢复

单机部署和共享集群部署的全库恢复：

```ebnf+diagram
syntax::= RESTORE DATABASE ([FROM TAG tag_name|DBID database_id] (UNTIL ((SCN integer)|(TIME time_string)))|FROM TAG tag_name)
[SKIP VALIDATE][OVERWRITE] [MAPPED FILE "mapfile_name"]
[restoreCommonSpecifier]
```

存算一体分布式集群部署的全库恢复：

```ebnf+diagram
syntax::= RESTORE CLUSTER FROM TAG tag_name
[MAPPED FILE "mapfile_name"]
[restoreCommonSpecifier]
```

使用本命令与[RESTORE DATABASE](../../../开发手册/SQL参考手册/SQL语句/RESTORE DATABASE)语句所执行的操作类似，但使用本命令恢复备份集后会启动当前节点至open状态，存算一体分布式集群部署中还会build备库（若存在）。

执行本命令恢复前，必须确保：

- 数据库当前版本与产生备份文件集时的版本完全一致。
- 所有节点上实例的数据文件目录、归档文件目录、bucket目录已清空，并启动至NOMOUNT状态。

	在存算一体分布式集群部署中，可执行[yasboot](../../yasboot/yasboot命令介绍/yasboot cluster) cluster clean **--restore**使集群环境满足该要求。

	> **Warn**:
	>
	> 请务必执行cluster clean **--restore**，未带--restore选项的clean命令会完全清除节点上的DATA目录且无法被恢复，如在BACKUP CLUSTER命令中使用了备份集的缺省路径，则位于DATA目录中的备份集也将被清除。

- 在共享集群/分布式集群部署中，执行恢复时连接的目标节点必须为MASTER ROLE节点（可通过查询视图V$INSTANCE的INSTANCE_ROLE字段确定），否则无法恢复数据库。

### FROM TAG

指定执行恢复的备份集，归档恢复中指定tag即只恢复该备份集中符合条件的归档日志备份文件。

### DBID

在PITR恢复时，搜索指定数据库ID的备份集，即恢复指定DATABASE_ID的数据库，DATABASE_ID可在V$DATABASE查询，在建库后DATABASE_ID不会发生变化，用来标识唯一的数据库。

### PITR恢复

UNTIL选项用于PITR恢复。当使用PITR恢复时，可以指定数据库备份集，也可以指定DBID自动搜索时间最近的数据库备份集。如果不指定TAG和DBID，将搜索时间最近目标点的任意备份集。

执行PITR恢复时，首先恢复数据库备份集，然后尝试恢复目标范围内的归档文件备份，最后尝试复用数据库本地的归档文件，尽可能恢复到目标点。因此如果目标点以内的归档文件全部在yasrman备份，或者在数据库本地都存在，都可以恢复到目标点。

PITR不适用于存算一体分布式集群部署。

#### UNTIL TIME

该语句用于将数据库恢复到此指定的时间点，即PITR恢复，time_string必须是时间格式的字符串，格式需与TIMESTAMP_FORMAT参数定义的格式一致。

指定的时间点必须在备份集生成时间之后。最终恢复到的时间，可能比目标小，但是误差在1s以内。

#### UNTIL SCN

该语句用于将数据库恢复到此指定的SCN号，即PITR恢复。指定的SCN号必须大于备份集中记录的SCN号。

示例（单机、共享集群部署）

```sql
-- 指定时间戳和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1' until time '2023-11-13'" -D /home/yashan/catalog

-- 指定时间戳和数据库ID
$ yasrman sys/********@192.168.1.2:1688 -c "restore database dbid 12573483 until time '2023-11-13 18:55:00'" -D /home/yashan/catalog

-- 仅指定时间戳
$ yasrman sys/********@192.168.1.2:1688 -c "restore database until time '2023-11-13 18:54:32:657892'" -D /home/yashan/catalog

-- 指定SCN
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1' until scn 499833430256541696" -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "restore database until scn 499833430256541696" -D /home/yashan/catalog
```

> **Note**: 
>
> - 最小时间点是保证数据库恢复到一致性的时间点，该时间点对应与备份完datafile，开始备份归档时的SCN对应的时间点，略小于备份完成时间。
> - 如果归档日志不够，或指定的时间点（或SCN）过大，回放完毕后也达不到指定的时间点，数据库会报错，用户可以查询V$DATABASE的current_scn来查看实际回放进度。如果有可用的归档日志副本，可以注册归档，重新执行restore操作。如果没有多余的归档，当前时间点是唯一的选择，继续执行resetlogs操作。
> - 当有多个数据库在同一个catalog备份时，执行PITR建议使用DBID区分不同数据库。

### MAPPED FILE

恢复命令中指定mapfile（备份集数据文件路径映射文件）路径，可通过该映射文件中的路径映射信息恢复对应的数据文件至指定位置，该映射文件可通过LIST命令生成，具体操作请查阅[LIST命令](./LIST)详解。

> **Note**: 
> 若RESTORE时指定路径转换文件，原始配置文件yasdb.ini中的路径转换参数将会失效。RESTORE执行完成后，如需建立备库，需重新配置yasdb.ini文件中的路径转换参数，否则会导致主备数据文件异常。
>
> 存算一体分布式集群部署中，若指定路径转换文件RESTORE的目标节点存在备节点，需要配置该节点下的路径主备路径转换参数，否则可能导致RESTORE操作失败。

### restoreCommonSpecifier

解密、并行度等通用恢复属性，请查阅[通用恢复属性](#restoreCommonSpecifier)。

示例（单机、共享集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'full_1'" -D /home/yashan/catalog
```

示例（存算一体分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore cluster from tag 'full_1'" -D /home/yashan/catalog
```

## 表空间恢复

```ebnf+diagram
syntax::= RESTORE TABLESPACE ((TBS_NAME) {"," (TBS_NAME)}) FROM TAG tag_name [CLEAN FILE] [SKIP VALIDATE] [OVERWRITE] 
[restoreCommonSpecifier]
```

表空间RESTORE操作仅适用于单机部署。

执行本命令恢复前，必须确保：

- 需保证数据库处于MOUNT或OPEN状态。
- 目标表空间必须处于OFFLINE状态，该表空间下的所有数据文件需处于OFFLINE或RECOVER状态。

表空间RESTORE完成后还需执行[RECOVER](RECOVER)操作对表空间对象文件执行一致性恢复。

### TABLESPACE

使用本关键字表示对表空间文件执行恢复操作。

TBS_NAME表示指定恢复的目标表空间。

### CLEAN FILE

使用该命令可在恢复过程中清理残留的原有表空间数据文件，若恢复命令中未指定该选项且存在待恢复的同名数据库文件则会恢复失败。

### restoreCommonSpecifier

解密、并行度等通用恢复属性，请查阅[通用恢复属性](#restoreCommonSpecifier)。

示例（单机部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore tablespace spc_test from tag 'spc_test_bak' " -D /home/yashan/catalog
```

## 归档日志恢复

```ebnf+diagram
syntax::= RESTORE ARCHIVELOG archivelogRangeSpecifier [FROM TAG tag_name] [SKIP VALIDATE][OVERWRITE] [restoreCommonSpecifier]
```

### ARCHIVELOG

使用本关键字表示对数据库归档文件执行恢复。

### archivelogRangeSpecifier

用于指定需恢复的归档日志范围，使用方法与规则同[RESTORE ARCHIVELOG](../../../开发手册/SQL参考手册/SQL语句/RESTORE ARCHIVELOG)语句中相关描述。

### FROM TAG

指定执行恢复的备份集，归档恢复中指定tag即只恢复该备份集中符合条件的归档日志备份文件。

### restoreCommonSpecifier

解密、并行度等通用恢复属性，请查阅[通用恢复属性](#restoreCommonSpecifier)。

示例（单机、共享集群部署）

### SKIP VALIDATE

指定该关键字用于跳过restore预检查。

预检查项包括目标路径是否可达、磁盘空间容量以及路径下是否存在同名文件等。

### OVERWRITE

指定该关键字用于覆盖同名文件，但REDO文件和归档文件可能用于PITR恢复故不作覆盖处理，若存在此类同名文件必须手动清理。

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "restore archivelog all " -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c "restore archivelog sequence between 10 and 20" -D /home/yashan/catalog
```
