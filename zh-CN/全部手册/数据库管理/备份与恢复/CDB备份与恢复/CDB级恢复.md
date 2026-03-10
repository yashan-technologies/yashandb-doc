## 恢复整个CDB



恢复数据库可以分为：

- 完整恢复整库：将全库备份的备份集解压、解密恢复到数据库目录，然后回放备份集中所有的归档日志将数据库恢复到一致性状态，可将数据库完整恢复到备份集对应的时刻。

- 基于时间点的恢复（PITR）：将全库备份的备份集解压、解密恢复到数据库目录，然后根据时间点回放归档日志：

    - 若目标时间点不晚于备份集对应的时刻，直接回放备份集中的归档日志直到目标时间点。
    
    - 若目标时间点晚于备份集对应的时刻，回放完该全库备份集中的所有归档日志后，再自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标点或回放完所有归档日志。





### 操作说明

- 整库恢复仅SYS用户才可执行。

- 如果数据库备份集采用加密备份，恢复时需指定解密关键字及其解密密码。

- 在单机一主多备部署中，在对主库（尤其是故障场景中仅剩孤立主库时）执行恢复操作前，需要先关闭自动降备功能（将HA_ELECTION_LEADER_LEASE_ENABLED设置为FALSE），避免因未及时检测到备库心跳而触发自动降备，待恢复完成后再按需还原对应配置。

- 共享集群/分布式集群部署中，只能在主实例（V$INSTANCE视图中INSTANCE_ROLE字段为MASTER_ROLE的实例）上执行数据库恢复操作。

- 如果LSC表在备份集后生成了slice文件，在以下场景基于时间点恢复整库时可能导致恢复后该LSC表存在数据不一致现象：

  - 数据库原来的slice文件全部存在，但是使用老的时间点恢复，此时该LSC表会多出部分slice文件。

  - 数据库原来的slice文件被清理，使用基于时间点的恢复，LSC表可能丢失存储在slice文件中的数据。

- 恢复前数据库环境准备：

  - 根容器及所有PDB处于NOMOUNT阶段。

  - 需清空相应的数据文件目录、归档文件目录以及bucket目录。

  - 如果备份集中存在加密对象，需先打开对应的密钥钱包文件，再进行恢复操作。

### 完整恢复整库

直接基于全库备份的备份集恢复数据库。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001'" \
-D /home/yashan/catalog
```



### 基于时间点恢复整库

执行基于时间点恢复整库时会先使用全库备份的备份集恢复数据，若未达到目标时间点，系统会自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标时间点或直至回放完最新一条日志。如果回放完所有归档仍未达到目标时间点，会提示`the database cannot recover to the target time, and the current time of the database is {实际可回放到的最近时间点}`，此种情况下启动数据库时需执行ALTER DATABASE OPEN RESETLOGS重置redo日志号。



示例（单机/共享集群/分布式集群部署）

```shell
# 指定时间戳和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# 指定时间戳和数据库ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE DBID 12573483 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# 指定SCN和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```

## 恢复根容器



恢复数据库可以分为：

- 完整恢复整库：将全库备份的备份集解压、解密恢复到数据库目录，然后回放备份集中所有的归档日志将数据库恢复到一致性状态，可将数据库完整恢复到备份集对应的时刻。

- 基于时间点的恢复（PITR）：将全库备份的备份集解压、解密恢复到数据库目录，然后根据时间点回放归档日志：

    - 若目标时间点不晚于备份集对应的时刻，直接回放备份集中的归档日志直到目标时间点。
    
    - 若目标时间点晚于备份集对应的时刻，回放完该全库备份集中的所有归档日志后，再自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标点或回放完所有归档日志。





### 操作说明

- 整库恢复仅SYS用户才可执行。

- 如果数据库备份集采用加密备份，恢复时需指定解密关键字及其解密密码。

- 在单机一主多备部署中，在对主库（尤其是故障场景中仅剩孤立主库时）执行恢复操作前，需要先关闭自动降备功能（将HA_ELECTION_LEADER_LEASE_ENABLED设置为FALSE），避免因未及时检测到备库心跳而触发自动降备，待恢复完成后再按需还原对应配置。

- 共享集群/分布式集群部署中，只能在主实例（V$INSTANCE视图中INSTANCE_ROLE字段为MASTER_ROLE的实例）上执行数据库恢复操作。

- 如果LSC表在备份集后生成了slice文件，在以下场景基于时间点恢复整库时可能导致恢复后该LSC表存在数据不一致现象：

  - 数据库原来的slice文件全部存在，但是使用老的时间点恢复，此时该LSC表会多出部分slice文件。

  - 数据库原来的slice文件被清理，使用基于时间点的恢复，LSC表可能丢失存储在slice文件中的数据。

- 恢复前数据库环境准备：

  - 根容器处于NOMOUNT阶段。

  - 需清空相应的数据文件目录、归档文件目录以及bucket目录。

  - 如果备份集中存在加密对象，需先打开对应的密钥钱包文件，再进行恢复操作。

### 完整恢复整库

直接基于全库备份的备份集恢复数据库。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup'" \
-D /home/yashan/catalog
```



### 基于时间点恢复整库

执行基于时间点恢复整库时会先使用全库备份的备份集恢复数据，若未达到目标时间点，系统会自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标时间点或直至回放完最新一条日志。如果回放完所有归档仍未达到目标时间点，会提示`the database cannot recover to the target time, and the current time of the database is {实际可回放到的最近时间点}`，此种情况下启动数据库时需执行ALTER DATABASE OPEN RESETLOGS重置redo日志号。



示例（单机/共享集群/分布式集群部署）

```shell
# 指定时间戳和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# 指定时间戳和数据库ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT DBID 12573483 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# 指定SCN和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```

## 恢复指定PDB



恢复数据库可以分为：

- 完整恢复整库：将全库备份的备份集解压、解密恢复到数据库目录，然后回放备份集中所有的归档日志将数据库恢复到一致性状态，可将数据库完整恢复到备份集对应的时刻。

- 基于时间点的恢复（PITR）：将全库备份的备份集解压、解密恢复到数据库目录，然后根据时间点回放归档日志：

    - 若目标时间点不晚于备份集对应的时刻，直接回放备份集中的归档日志直到目标时间点。
    
    - 若目标时间点晚于备份集对应的时刻，回放完该全库备份集中的所有归档日志后，再自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标点或回放完所有归档日志。





### 操作说明

- 整库恢复仅SYS用户才可执行。

- 如果数据库备份集采用加密备份，恢复时需指定解密关键字及其解密密码。

- 在单机一主多备部署中，在对主库（尤其是故障场景中仅剩孤立主库时）执行恢复操作前，需要先关闭自动降备功能（将HA_ELECTION_LEADER_LEASE_ENABLED设置为FALSE），避免因未及时检测到备库心跳而触发自动降备，待恢复完成后再按需还原对应配置。

- 共享集群/分布式集群部署中，只能在主实例（V$INSTANCE视图中INSTANCE_ROLE字段为MASTER_ROLE的实例）上执行数据库恢复操作。

- 如果LSC表在备份集后生成了slice文件，在以下场景基于时间点恢复整库时可能导致恢复后该LSC表存在数据不一致现象：

  - 数据库原来的slice文件全部存在，但是使用老的时间点恢复，此时该LSC表会多出部分slice文件。

  - 数据库原来的slice文件被清理，使用基于时间点的恢复，LSC表可能丢失存储在slice文件中的数据。

- 恢复前数据库环境准备：

  - 根容器及指定PDB处于NOMOUNT阶段。

  - 需清空相应的数据文件目录、归档文件目录以及bucket目录。

  - 如果备份集中存在加密对象，需先打开对应的密钥钱包文件，再进行恢复操作。

### 完整恢复整库

直接基于全库备份的备份集恢复数据库。



示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup'" \
-D /home/yashan/catalog
```



### 基于时间点恢复整库

执行基于时间点恢复整库时会先使用全库备份的备份集恢复数据，若未达到目标时间点，系统会自动查找符合条件的归档日志文件（或其备份集）进行回放，直到达到目标时间点或直至回放完最新一条日志。如果回放完所有归档仍未达到目标时间点，会提示`the database cannot recover to the target time, and the current time of the database is {实际可回放到的最近时间点}`，此种情况下启动数据库时需执行ALTER DATABASE OPEN RESETLOGS重置redo日志号。



示例（单机/共享集群/分布式集群部署）

```shell
# 指定时间戳和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# 指定时间戳和数据库ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 DBID 29401764 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# 指定SCN和数据库备份集
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```
