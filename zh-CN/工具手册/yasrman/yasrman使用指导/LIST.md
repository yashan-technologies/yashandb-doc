LIST命令用于显示yasrman存储库中记录的备份集信息。

## 列出备份集信息

```ebnf
= LIST BACKUP [[(TAG tag_name)] [(DETAIL [FORMAT XML])|(NODE INFO TO "nodeinfo.txt")|(DATABASE CONFIGURE PARAMETER)] | (OF PLUGGABLE DATABASE pdb_name)].
```

通过指定备份集别名查看所有节点上的该备份集信息，不指定TAG时将展示所有节点上的所有备份集。

### DETAIL

指定DETAIL列出备份集详细信息，默认以XML格式显示。

### NODE INFO

列出存算一体分布式集群部署中备份的节点部署详细信息输出至指定的临时文件nodeinfo.txt中，具体表现内容如下示例。

```shell
node_id=1-1, node_type=MN, role=PRIMARY, endpoint=1, data_path=/data/shm/data/mn-1-1
node_id=2-1, node_type=CN, role=PRIMARY, endpoint=2, data_path=/data/shm/data/cn-2-1
node_id=3-1, node_type=DN, role=PRIMARY, endpoint=3, data_path=/data/shm/data/dn-3-1
node_id=4-1, node_type=DN, role=PRIMARY, endpoint=4, data_path=/data/shm/data/dn-4-1
node_id=5-1, node_type=DN, role=PRIMARY, endpoint=5, data_path=/data/shm/data/dn-5-1
```

### DATABASE CONFIGURE PARAMETER

列出存算一体分布式集群部署中备份集各个节点的配置文件的备份信息，具体表现内容如下示例。

```shell
  backupset ID: 0, node 1-1 
    configure backup path: /home/yashan/catalog/backup/bak1/MN-1-1_cfg.bak 
  backupset ID: 1, node 2-1 
    configure backup path: /home/yashan/catalog/backup/bak1/CN-2-1_cfg.bak 
  backupset ID: 2, node 3-1 
    configure backup path: /home/yashan/catalog/backup/bak1/DN-3-1_cfg.bak 
  backupset ID: 3, node 4-1 
    configure backup path: /home/yashan/catalog/backup/bak1/DN-4-1_cfg.bak 
  backupset ID: 4, node 5-1 
    configure backup path: /home/yashan/catalog/backup/bak1/DN-5-1_cfg.bak 
```

### OF PLUGGABLE DATABASE

该语句仅适用于容器数据库，用于列出指定PDB的备份集信息，完整语句为`LIST BACKUP OF PLUGGABLE DATABASE pdb_name`，无法与其他LIST语法同时使用。

示例（单机/共享集群/分布式集群部署）

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "list backup tag 'full_1'" -D /home/yashan/catalog
Group: type DATABASE, tag: full_1, format: /home/yashan/catalog/backup/full_bak_1, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 0
    backupset key: 3407244139, base key: 0, restore time: 2025-07-03 15:45:18.966827 (UTC+08:00), 
	backup path: /home/yashan/catalog/backup/full_bak_1 
    instance: 1, archive range sequence: 4-4 
                  scn: 711441947081232384-711442105212928000 

$ yasrman sys/********@192.168.1.2:1688 -c "list backup tag 'arch_all'" -D /home/yashan/catalog
Group: type ARCHIVE, tag: arch_all, format: /home/yashan/catalog/backup/arch_all, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 24576
    backupset key: 408660232, base key: 0, restore time: 2025-07-03 15:45:18.966827 (UTC+08:00), 
	backup path: /home/yashan/catalog/backup/arch_all
    instance: 1, archive range sequence: 3-4 
                  scn: 711441943202398208-711442105212928000


# 以下示例命令仅适用于容器数据库
$ yasrman sys/********@192.168.1.2:1688 -c "list backup of pluggable database pdb1" -D /home/yashan/catalog
Group: type DATABASE, tag: bak_root_full_client_001, format: /home/yashan/catalog/backup/bak_root_full_client_001, connect url: 192.168.1.2:1688, nodeCount: 5, distribution: FALSE, isClient: TRUE, offset: 0
    backupset key: 2052469490, base key: 0, restore time: 0, pdbName: PDB1, uuid: C236009D82FBC994254FDCE1A20FE59B, container id: 2, backup path: /home/yashan/catalog/backup/bak_root_full_client_001/PDB_PDB1 
     instance: 1, archive range sequence: 3-4 
                  scn: 780742145869746178-780742162009128962 

Group: type DATABASE, tag: bak_pdb1, format: /home/yashan/catalog/backup/bak_pdb1, connect url: 192.168.1.2:1688, nodeCount: 5, distribution: FALSE, isClient: TRUE, offset: 45056
    backupset key: 3720986461, base key: 0, restore time: 0, pdbName: PDB1, uuid: C236009D82FBC994254FDCE1A20FE59B, container id: 2, backup path: /home/yashan/catalog/backup/bak_pdb1/PDB_PDB1 
     instance: 1, archive range sequence: 5-5 
                  scn: 780742162009128962-780742190591393794 
```

> **Note**: 
>
> list命令仅列出指定的catalog文件中存在的备份集元数据信息。由于catalog不会与数据库自动同步备份元信息，因此备份集元数据信息可能与数据库中DBA_BACKUP_SET视图内容不完全一致。
> 
> 不指定DETAIL字段只列出备份概要信息，指定DETAIL字段默认以XML格式输出备份集详细内容。

以下为XML格式输出的备份集详细信息示例以及每个字段的含义说明。
```shell
<?xml version="1.0" encoding="UTF-8"?>
<backupsets>
	<group>
		<backup_type>DB_INCR</backup_type> # 备份类型，此处表示数据库增量备份
		<backup_tag>bak_incr1</backup_tag> # 备份集唯一标识
		<backup_path>/home/yashan/backup/bak_incr1</backup_path>  # 备份集路径
		<connect_url>192.168.1.2:1688</connect_url> # 执行备份的URL
		<node_count>1</node_count> # 存算一体分布式集群部署中备份集里的节点个数
		<is_distribution>FALSE</is_distribution> # 是否为存算一体分布式集群部署的数据库备份集
		<is_rman_side>FALSE</is_rman_side> # 是否在yasrman所在设备保存的备份集
		<dbinfo>
			<database_create_time>2025-07-03 23:11:04.750852 (UTC+08:00)</database_create_time> # 数据库创建时间
			<database_id>3441381755</database_id> # 数据库唯一ID
			<restore_time>NULL</restore_time> # 数据库最近一次restore的时间，为NULL表示数据库没有执行过restore
			<instance_count>1</instance_count> # 数据库实例个数
			<instance_map>1</instance_map> # 数据库备份时，活跃实例的map
		</dbinfo>
		<backupset>
			<start_time>2025-07-03 23:11:48.329668 (UTC+08:00)</start_time> # 备份开始时间
			<complete_time>2025-07-03 23:11:49.345991 (UTC+08:00)</complete_time> # 备份结束时间
			<backup_path>/home/yashan/backup/bak_incr1</backup_path> # 备份集路径
			<base_backup_path>/home/yashan/backup/bak_incr0</base_backup_path> # 对于增量备份集，该值表示基线备份集的路径
			<base_lsn>6028</base_lsn> # 增量备份集的基线lsn
			<backup_type>DB_INCR</backup_type> # 备份类型
			<backup_level>1</backup_level> # 增量备份集的level
			<file_count>12</file_count> # 备份的文件总数
			<recover_begin>0-8-4-3275</recover_begin> # 备份集的回放开始点
			<flush_point>0-8-4-3275</flush_point> # 备份集至少要回放到日志点
			<reset_point>0-0-0-0</reset_point> # 执行resetlogs对应的日志点，为0表示没有做过resetlogs操作
			<trunc_lsn>6032</trunc_lsn> # 截断lsn，表示小于该lsn的所有数据页面都已经在备份集备份
			<flush_lsn>6032</flush_lsn> # 刷盘lsn，表示备份完数据文件后，数据库的lsn
			<consistence_scn>527444098809229312</consistence_scn> # 一致性scn，表示改备份集要回放redo或归档日志到该scn之后，数据库才能保证一致性
			<base_lfn>3271</base_lfn> # 增量备份集的基线lfn
			<encryption_algorithm>AES128</encryption_algorithm> # 加密算法
			<input_bytes>411283456</input_bytes> # 总的读IO大小
			<output_bytes>16792576</output_bytes> # 总的写IO大小
			<backup_tag>bak_incr1</backup_tag> # 备份集唯一标识
			<thread_id>0</thread_id> # 备份实例的ID
			<is_base_tag_incr_bak>FALSE</is_base_tag_incr_bak> # 是否为手动指定基线备份集的增量备份
			<instance>
				<instance_id>1</instance_id> # 实例ID
				<sequence_start>8</sequence_start> # 该实例第一个归档备份文件的asn
				<sequence_end>8</sequence_end> # 该实例最后一个归档备份文件的asn
			</instance>
			<scn_start>527444076943417344</scn_start> # 备份集所有归档里最小的scn
			<scn_end>527444098809229312</scn_end> # 备份集所有归档里最大的scn
			<create_time>2025-07-03 23:11:04.750852 (UTC+08:00)</create_time> # 备份目标数据库的建库时间
			<database_version>571</database_version> # 备份目标数据库的版本
			<spc_import_scn>0</spc_import_scn> # 若发生过表空间迁移，该SCN更新为所有新迁移表空间的最大SCN
			<spc_import_lsn>0</spc_import_lsn> # 若发生过表空间迁移，该LSN更新为所有新迁移表空间的最大LSN
		</backupset>
	</group>
</backupsets>
```

<span id="LISTBACKUPSET" name="LISTBACKUPSET"></span>

## 生成路径映射文件

执行数据库恢复时，默认将所有文件恢复至原路径。如需调整数据文件或REDO日志文件存放位置（例如增配了高性能磁盘后将高频数据文件改存至新磁盘中），可以先根据规划准备路径映射文件再基于此映射文件进行恢复，此时相应的数据文件或REDO日志文件将会存放至新的路径（target_path）。

生成路径映射文件的主要步骤包括：

1. 将目标备份集中的数据文件原始路径罗列至路径映射文件中。

2. 编辑路径映射文件，填写`target_path`（或形如`xxx_target_path`）字段，为目标文件指定新的存放路径。

### 罗列原始路径

```ebnf
= LIST BACKUPSET TAG tag_name [OF PLUGGABLE DATABASE pdb_name] MAPPED FILE mapfile_name.
```

该命令用于将目标备份集中的所有数据文件路径信息写入至路径映射文件中。

#### TAG tag_name

根据TAG指定目标备份集，必须为已存在的tag_name。

#### OF PLUGGABLE DATABASE

仅适用于容器数据库，用于指定目标PDB，表示仅将目标备份集中属于该PDB的所有数据文件路径信息写入至mapfile文件中。

#### mapfile_name

指定路径映射文件的存放路径与文件名，必须指定为可访问的绝对路径。

### 配置目标路径

执行LIST BACKUPSET命令生成的路径映射文件内容示例如下：

```text
database_node_id: 1-1   # 数据库节点ID，不可手动更改。
db_origin_home:/data/yashan/yasdb_data/db-1-1  # 备份集记录的原始的DATA目录，不可手动更改。
db_target_home:   		# 如需调整数据库恢复后的DATA目录，可手动填写该值，将其指定为文件夹级别的路径。
redo_target_path: 	    # 如需调整数据库REDO日志文件的恢复路径，可手动填写该值，将其指定为文件夹级别的路径。

# 在存算一体分布式集群部署中无后续内容

tablespace_id:0, datafile_id:0  	# 数据库文件所属表空间ID和其数据文件的ID，不可手动更改。
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/system		# 备份集记录的数据文件原始路径（文件名+绝对路径），不可手动更改。
target_path:                        # 如需调整数据文件的恢复路径，可手动填写该值，必须指定为绝对路径且文件名必须和原始文件名一致。

tablespace_id:1, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/sysaux
target_path:

tablespace_id:2, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/temp
target_path:

tablespace_id:3, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/swap
target_path:

tablespace_id:4, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/users
target_path:/data/users

tablespace_id:5, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/undo
target_path:

tablespace_id:6, datafile_id:0
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/tps_test_yasrman
target_path:
```

编辑路径映射文件时，仅允许填写`target_path`（或形如`xxx_target_path`）字段，且不得删除或增加任意行，详细路径转换规则如下：

- 控制文件和归档日志文件始终会恢复至原始路径，不参与路径转换。

- 当YashanDB部署为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）时，整个CDB的路径映射文件仅`db_target_home`和`redo_target_path`字段值有意义，数据文件对应的`target_path`字段始终不生效。

- 在存算一体分布式集群部署中，该文件不存在表空间相关信息，仅可配置`db_target_home`和`redo_target_path`字段值。

- 其他场景中，数据文件的target_path配置生效优先级高于数据库节点的db_target_home配置。同一个数据库节点下，路径配置与生效规则如下：

	| db_target_home | 数据文件target_path | 数据文件的实际恢复路径|
	| -------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
	| 指定           | 指定                                                      | 恢复至对应target_path                                        |
	| 不指定         | 指定                                                      | 恢复至对应target_path                                        |
	| 指定           | 不指定                                                    | 在db_target_home下自动创建dbfiles文件夹，并恢复至`{db_target_home}/dbfiles`中 |



