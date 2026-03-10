## 备份整个CDB

### 数据库备份



数据库备份会备份其控制文件、数据文件、redo日志文件以及部分归档日志文件。

#### 操作说明

- 备份操作仅SYS超级用户或拥有SYSDBA、SYSBACKUP权限的全局用户才可执行。

- 备份数据库不允许和表空间、redo文件等数据文件的增删、resize操作并发。在备库执行备份操作时还会阻塞备库回放表空间、数据文件操作相关的redo，此时会导致主备的日志差距增大，需等待备库的备份操作完成后备库才会正常回放主库redo。

- 若在数据库只读模式或异常状态下执行全量备份，可以指定FORCE关键字，但强制备份不会记录系统表。

- 备份前数据库环境准备：

    - 根容器及所有PDB处于OPEN阶段且已开启归档模式。

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

    - 如需备份加密对象，需先手动备份密钥钱包文件。

 



#### 全量备份

全量备份是指对某一时间点上的所有数据进行完全复制，不依赖之前的备份集。一个全量备份集可以恢复出所有数据。指定FULL关键字时表示执行全量备份。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_backup' FULL FORMAT 'CDB_full_001' PARALLELISM 3 DEST SERVER" \
-D /home/yashan/catalog
```



#### 增量备份

增量备份是指首次执行基线备份（LEVEL 0），后续每次只需备份增量数据（LEVEL 1），备份LEVEL 1时可以基于LEVEL 0执行（即累积增量备份），也可以基于前序LEVEL 1执行（即差异增量备份）。

指定INCREMENTAL关键字表示执行增量备份。

增量备份需遵循如下使用规则：

- 同一个增量备份链路中，连续LEVEL 1增量备份次数不得超过1000次。

- 同一个增量备份链路中的多个备份集允许采用不同的压缩策略。

- 同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。

- 同一个增量备份链路中，DEST选项必须保持一致。

- 恢复时需要依次恢复基线备份集、增量备份集。



##### LEVEL 0

示例（单机、共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'CDB_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'CDB_incr_1_002'" \
-D /home/yashan/catalog
```



#### 压缩备份

执行数据库备份时，可以通过COMPRESSION关键字指定备份集压缩策略，可选项包括：

- ALGORITHM关键字指定压缩算法：ZSTD或LZ4。

- 压缩级别：HIGH、MEDIUM或LOW。

- COMPRESSION后的选项都省略时，默认按ZSTD算法和LOW级别。

同一个增量备份链路中的多个备份集允许采用不同的压缩策略。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### 加密备份

执行数据库备份时，可以通过ENCRYPTION关键字指定备份集加密策略，选项包括：

- 加密算法：AES128、AES192、AES256或SM4，省略时默认采用SM4。

- IDENTIFIED BY关键字指定指定密钥。

同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE TAG 'CDB_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围指定方式以及压缩、加密等通用备份属性与使用SQL语句备份一致。

 

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
-c "BACKUP ARCHIVELOG TIME BETWEEN '2025-08-28 08:14:01' AND '2025-09-28 11:14:01'  FORMAT 'CDB_Arch_01' TAG 'CDB_Arch' COMPRESSION;" \
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

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

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

- 同一个增量备份链路中，DEST选项必须保持一致。

- 恢复时需要依次恢复基线备份集、增量备份集。



##### LEVEL 0

示例（单机、共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'root_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'root_incr_1_002'" \
-D /home/yashan/catalog
```



#### 压缩备份

执行数据库备份时，可以通过COMPRESSION关键字指定备份集压缩策略，可选项包括：

- ALGORITHM关键字指定压缩算法：ZSTD或LZ4。

- 压缩级别：HIGH、MEDIUM或LOW。

- COMPRESSION后的选项都省略时，默认按ZSTD算法和LOW级别。

同一个增量备份链路中的多个备份集允许采用不同的压缩策略。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### 加密备份

执行数据库备份时，可以通过ENCRYPTION关键字指定备份集加密策略，选项包括：

- 加密算法：AES128、AES192、AES256或SM4，省略时默认采用SM4。

- IDENTIFIED BY关键字指定指定密钥。

同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP DATABASE ROOT TAG 'root_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围指定方式以及压缩、加密等通用备份属性与使用SQL语句备份一致。

 

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
-c "BACKUP ARCHIVELOG ROOT TIME BETWEEN '2025-08-28 08:14:01' AND '2025-09-28 11:14:01'  FORMAT 'root_Arch_01' TAG 'root_Arch' COMPRESSION;" \
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

    - 已创建catalog路径，具体操作请查阅[CREATE CATALOG](../../../工具手册/yasrman/yasrman使用指导/CREATE CATALOG)。

    - 如需自定义指定备份文件的保存路径，必须确保该路径为空且磁盘空间充足。

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

- 同一个增量备份链路中，DEST选项必须保持一致。

- 恢复时需要依次恢复基线备份集、增量备份集。



##### LEVEL 0

示例（单机、共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_0_backup' INCREMENTAL LEVEL 0 FORMAT 'pdb1_incr_0_002'" \
-D /home/yashan/catalog
```

##### LEVEL 1

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_incr_1_backup' INCREMENTAL LEVEL 1 FORMAT 'pdb1_incr_1_002'" \
-D /home/yashan/catalog
```



#### 压缩备份

执行数据库备份时，可以通过COMPRESSION关键字指定备份集压缩策略，可选项包括：

- ALGORITHM关键字指定压缩算法：ZSTD或LZ4。

- 压缩级别：HIGH、MEDIUM或LOW。

- COMPRESSION后的选项都省略时，默认按ZSTD算法和LOW级别。

同一个增量备份链路中的多个备份集允许采用不同的压缩策略。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_compress' COMPRESSION ALGORITHM ZSTD LOW" \
-D /home/yashan/catalog
```



#### 加密备份

执行数据库备份时，可以通过ENCRYPTION关键字指定备份集加密策略，选项包括：

- 加密算法：AES128、AES192、AES256或SM4，省略时默认采用SM4。

- IDENTIFIED BY关键字指定指定密钥。

同一个增量备份链路中的多个备份集必须采用一致的加密策略（都加密或都不加密，都加密时密码相同），但可以采用不同的加密算法。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "BACKUP PLUGGABLE DATABASE pdb1 TAG 'pdb1_full_encryption' ENCRYPTION AES256 IDENTIFIED BY yas2022" \
-D /home/yashan/catalog
```

### 归档日志备份

备份范围仅支持通过ALL、UNTIL TIME或UNTIL SCN指定，压缩、加密等通用备份属性则与使用SQL语句备份一致。

 

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
