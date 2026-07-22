在执行备份操作前，可先对以下[通用属性](../../../工具手册/yasrman/yasrman使用指导/BACKUP.md#backupCommonSpecifier)进行规划，再根据所需备份的数据库对象选择合适的操作。

|  属性| 配置说明| 默认配置|
|--------------------|--------------|-------|
|  压缩   | 通过COMPRESSION关键字指定，包括压缩算法和压缩级别：<br />* 压缩算法：通过ALGORITHM关键字指定，支持ZSTD和LZ4。<br />* 压缩级别：支持LOW、MEDIUM和HIGH。 | * 默认不压缩。<br />* 指定COMPRESSION关键字但其子属性选项均省略时，默认采用ZSTD算法和LOW级别。 |
|  加密   | 通过ENCRYPTION关键字指定，包括加密算法和密钥：<br />* 加密算法：支持AES128、AES192、AES256和SM4。<br />* 密钥：通过IDENTIFIED BY关键字指定，密钥的约束规则同[数据库用户的密码规则](../../../开发手册/SQL参考手册/SQL语句/CREATE USER.md#password)。 | * 默认不加密。<br />* 指定ENCRYPTION关键字但不明确指定加密算法时，默认采用SM4算法。 |
|  并行度   | 通过PARALLELISM关键字指定，取值范围为[1,16]。 | 2 |
| 备份目的端 | 通过DEST关键字指定<br />* DEST SERVER：表示备份到数据库服务器端，即备份集保存在数据库所在服务器（共享集群/分布式集群部署中可通过FORMAT指定YFS路径将备份集文件存放至共享存储）。<br />* DEST CLIENT：表示备份到工具端，即备份集保存在yasrman所在服务器。 | SERVER |
| 备份集文件名 | 通过FORMAT关键字指定，可包含文件存放路径和文件名。<br />在共享集群/分布式集群部署中，还可以指定为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径将备份集保存至共享存储。 | 取决于备份目的端：<br />* DEST SERVER：`$YASDB_DATA/backup/bak_{date}`<br />* DEST CLIENT：`catalog/backup/bak_{date}` |
|  文件分片规格   | 通过SECTION SIZE关键字指定，超过该值的文件会被拆分为多个小文件执行备份，取值范围为[128M,32T]。<br />若指定的数值不是1M的整数倍，会向下取整按1M对齐。 | 系统自动计算 |

## 备份整个CDB

### 数据库备份



数据库备份会备份其控制文件、数据文件、redo日志文件以及部分归档日志文件。

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份数据库不允许和表空间、redo文件等数据文件的增删、resize操作并发。在备库执行备份操作时还会阻塞备库回放表空间、数据文件操作相关的redo，此时会导致主备的日志差距增大，需等待备库的备份操作完成后备库才会正常回放主库redo。

- 若在数据库只读模式或异常状态下执行全量备份，可以指定FORCE关键字，但强制备份不会记录系统表。

- 备份前数据库环境准备：

    - 根容器及所有PDB处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG.md)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径磁盘空间充足。若目标路径下已有备份集文件，再次备份时建议指定为不同的文件名，避免覆盖原有文件。

    - 如需备份加密对象，需先手动备份密钥钱包文件。

 



#### 全量备份

全量备份是指对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。指定FULL关键字时表示执行全量备份。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_backup' FULL FORMAT 'CDB_full_001' COMPRESSION ALGORITHM ZSTD LOW PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### 增量备份

增量备份是指首次执行基线备份（LEVEL 0），后续每次只需备份增量数据（LEVEL 1），备份LEVEL 1时可以基于LEVEL 0执行（即累积增量备份），也可以基于前序LEVEL 1执行（即差异增量备份）。

指定INCREMENTAL关键字表示执行增量备份。

增量备份需遵循如下使用规则：

- 同一个增量备份链路中，连续LEVEL 1增量备份次数不得超过1000次。

- 同一个增量备份链路中的多个备份集允许采用不同的压缩策略。

- 同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。

- 同一个增量备份链路中的备份集必须放在同一台设备上。

- 恢复时需要依次恢复基线备份集、增量备份集。



示例（单机、共享集群/分布式集群部署）

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'CDB_incr_0_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

#LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'CDB_incr_1_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围仅支持通过ALL、UNTIL TIME或UNTIL SCN指定。

 

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份归档日志时指定备份范围可以精确到某一条具体日志，但实际会以文件为最小单位进行备份，即会备份目标日志所在的整个文件。

- 备份前数据库环境准备：

    - 根容器及所有PDB处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

#### 操作示例

 

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP ARCHIVELOG ALL FORMAT 'CDB_Arch_01' TAG 'CDB_Arch' COMPRESSION" \
-D /home/yashan/catalog
```

## 备份根容器

### 数据库备份



数据库备份会备份其控制文件、数据文件、redo日志文件以及部分归档日志文件。

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份数据库不允许和表空间、redo文件等数据文件的增删、resize操作并发。在备库执行备份操作时还会阻塞备库回放表空间、数据文件操作相关的redo，此时会导致主备的日志差距增大，需等待备库的备份操作完成后备库才会正常回放主库redo。

- 若在数据库只读模式或异常状态下执行全量备份，可以指定FORCE关键字，但强制备份不会记录系统表。

- 备份前数据库环境准备：

    - 根容器处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG.md)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径磁盘空间充足。若目标路径下已有备份集文件，再次备份时建议指定为不同的文件名，避免覆盖原有文件。

    - 如需备份加密对象，需先手动备份密钥钱包文件。

 



#### 全量备份

全量备份是指对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。指定FULL关键字时表示执行全量备份。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_backup' FULL FORMAT 'root_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### 增量备份

增量备份是指首次执行基线备份（LEVEL 0），后续每次只需备份增量数据（LEVEL 1），备份LEVEL 1时可以基于LEVEL 0执行（即累积增量备份），也可以基于前序LEVEL 1执行（即差异增量备份）。

指定INCREMENTAL关键字表示执行增量备份。

增量备份需遵循如下使用规则：

- 同一个增量备份链路中，连续LEVEL 1增量备份次数不得超过1000次。

- 同一个增量备份链路中的多个备份集允许采用不同的压缩策略。

- 同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。

- 同一个增量备份链路中的备份集必须放在同一台设备上。

- 恢复时需要依次恢复基线备份集、增量备份集。




示例（单机、共享集群/分布式集群部署）

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'root_incr_0_002' COMPRESSION ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

# LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'root_incr_1_002' COMPRESSION ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围仅支持通过ALL、UNTIL TIME或UNTIL SCN指定。

 

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份归档日志时指定备份范围可以精确到某一条具体日志，但实际会以文件为最小单位进行备份，即会备份目标日志所在的整个文件。

- 备份前数据库环境准备：

    - 根容器处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

#### 操作示例

 

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP ARCHIVELOG ROOT UNTIL TIME '2026-01-09 14:20:54.336905' FORMAT 'root_Arch_01' TAG 'root_Arch' COMPRESSION;" \
-D /home/yashan/catalog
```

## 备份指定PDB

### 数据库备份



数据库备份会备份其控制文件、数据文件、redo日志文件以及部分归档日志文件。

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份数据库不允许和表空间、redo文件等数据文件的增删、resize操作并发。在备库执行备份操作时还会阻塞备库回放表空间、数据文件操作相关的redo，此时会导致主备的日志差距增大，需等待备库的备份操作完成后备库才会正常回放主库redo。

- 若在数据库只读模式或异常状态下执行全量备份，可以指定FORCE关键字，但强制备份不会记录系统表。

- 备份前数据库环境准备：

    - 根容器及指定PDB处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG.md)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径磁盘空间充足。若目标路径下已有备份集文件，再次备份时建议指定为不同的文件名，避免覆盖原有文件。

    - 如需备份加密对象，需先手动备份密钥钱包文件。

 



#### 全量备份

全量备份是指对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。指定FULL关键字时表示执行全量备份。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_backup' FULL FORMAT 'pdb1_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### 增量备份

增量备份是指首次执行基线备份（LEVEL 0），后续每次只需备份增量数据（LEVEL 1），备份LEVEL 1时可以基于LEVEL 0执行（即累积增量备份），也可以基于前序LEVEL 1执行（即差异增量备份）。

指定INCREMENTAL关键字表示执行增量备份。

增量备份需遵循如下使用规则：

- 同一个增量备份链路中，连续LEVEL 1增量备份次数不得超过1000次。

- 同一个增量备份链路中的多个备份集允许采用不同的压缩策略。

- 同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。

- 同一个增量备份链路中的备份集必须放在同一台设备上。

- 恢复时需要依次恢复基线备份集、增量备份集。



示例（单机、共享集群/分布式集群部署）

```shell
# LEVEL 0
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'pdb1_incr_0_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog

# LEVEL 1
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'pdb1_incr_1_002' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围仅支持通过ALL、UNTIL TIME或UNTIL SCN指定。

 

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份归档日志时指定备份范围可以精确到某一条具体日志，但实际会以文件为最小单位进行备份，即会备份目标日志所在的整个文件。

- 备份前数据库环境准备：

    - 根容器及指定PDB处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

#### 操作示例

 

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE ARCHIVELOG pdb1 ALL FORMAT 'pdb1_Arch_01' TAG 'pdb1_Arch' COMPRESSION;" \
-D /home/yashan/catalog
```
