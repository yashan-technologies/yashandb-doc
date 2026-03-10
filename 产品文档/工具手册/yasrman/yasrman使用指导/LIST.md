LIST命令用于显示yasrman存储库中记录的备份集信息。

## 列出备份集信息

```ebnf+diagram
syntax::= LIST BACKUP [TAG tag_name] [(DETAIL [FORMAT XML])|(NODE INFO TO "nodeinfo.txt")|(DATABASE CONFIGURE PARAMETER)]
```
通过指定备份集别名查看所有节点上的该备份集信息，不指定TAG时将展示所有节点上的所有备份集。

### DETAIL

指定DETAIL列出备份集详细信息，默认以XML格式显示。

### NODE INFO

列出分布式部署中备份的节点部署详细信息输出至指定的临时文件nodeinfo.txt中，具体表现内容如下示例。

```shell
node_id=1-1, node_type=MN, role=PRIMARY, endpoint=1, data_path=/data/shm/data/mn-1-1
node_id=2-1, node_type=CN, role=PRIMARY, endpoint=2, data_path=/data/shm/data/cn-2-1
node_id=3-1, node_type=DN, role=PRIMARY, endpoint=3, data_path=/data/shm/data/dn-3-1
node_id=4-1, node_type=DN, role=PRIMARY, endpoint=4, data_path=/data/shm/data/dn-4-1
node_id=5-1, node_type=DN, role=PRIMARY, endpoint=5, data_path=/data/shm/data/dn-5-1
```

### DATABASE CONFIGURE PARAMETER

列出分布式部署中备份集各个节点的配置文件的备份信息，具体表现内容如下示例。

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

示例

```shell
$ yasrman sys/sys@192.168.1.2:1688 -c "list backup tag 'full_1'" -D /home/yashan/catalog
Group: type DB, tag: full_1, format: /home/yashan/catalog/backup/full_bak_1, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 0
    backup path: /home/yashan/catalog/backup/full_bak_1
    instance: 0, archive range  sequence: 14-14 
                   scn: 524931827025387520-524931831243571200 

$ yasrman sys/sys@192.168.1.2:1688 -c "list backup tag 'arch_all'" -D /home/yashan/catalog
Group: type ARCHIVE, tag: arch_all, format: /data/regress/ha_regress/ha_home/catalog/backup/arch_all, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 0
    backup path: /data/regress/ha_regress/ha_home/catalog/backup/arch_all
     instance: 0, archive range  sequence: 21-33 
                   scn: 524931839394082816-524931866077937664 
```

> **Note**：
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
		<node_count>1</node_count> # 分布式部署中备份集里的节点个数
		<is_distribution>FALSE</is_distribution> # 是否为分布式部署的数据库备份集
		<is_rman_side>FALSE</is_rman_side> # 是否在yasrman所在设备保存的备份集
		<dbinfo>
			<database_create_time>2024-01-30 17:34:39</database_create_time> # 数据库创建时间
			<database_id>3441381755</database_id> # 数据库唯一ID
			<restore_time>NULL</restore_time> # 数据库最近一次restore的时间，为NULL表示数据库没有执行过restore
			<instance_count>1</instance_count> # 数据库实例个数
			<instance_map>1</instance_map> # 数据库备份时，活跃实例的map
		</dbinfo>
		<backupset>
			<start_time>2024-01-30 17:35:31</start_time> # 备份开始时间
			<complete_time>2024-01-30 17:35:32</complete_time> # 备份结束时间
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
			<create_time>2024-04-08 16:22:23</create_time> # 备份目标数据库的建库时间
			<database_version>571</database_version> # 备份目标数据库的版本
			<spc_import_scn>0</spc_import_scn> # 若发生过表空间迁移，该SCN更新为所有新迁移表空间的最大SCN
			<spc_import_lsn>0</spc_import_lsn> # 若发生过表空间迁移，该LSN更新为所有新迁移表空间的最大LSN
		</backupset>
	</group>
</backupsets>
```

<span id="LISTBACKUPSET" name="LISTBACKUPSET" class="yaslink"></span>

## 列出备份集文件路径信息

```ebnf+diagram
syntax::= LIST BACKUPSET TAG tag_name MAPPED FILE mapfile_name
```
使用list命令指定备份集tag，列出该tag对应的备份集中的所有数据文件路径信息至mapfile文件中。


mapfile_name必须指定为可访问的绝对路径。

mapfile文件中的target_path选项允许手动修改，后续在执行RESTORE操作命令指定该mapfile文件，恢复对应的数据文件至相应的target_path路径中。除target_path选项外的其他内容均不可手动修改，否则可能导致RESTORE操作失败或者数据异常。

mapfile内容示例如下：
```shell
database_node_id: 1-1  # 数据库节点ID，分布式部署中用该ID确定该节点的恢复路径，不可手动更改。
db_origin_home:/data/YASDB_DATA/node_1  # 生成备份集的原始数据库HOME路径，不可手动更改。
db_target_home:/data/NEW_YASDB_DATA  # 指定数据库文件的目标恢复路径，需指定为文件夹级别。
redo_target_path:/data/YASDB_DATA/rdhome  # 指定数据库的REDO文件恢复路径。

tablespace_id:0, datafile_id:0  # 数据库文件所属表空间ID和其数据文件的ID，不可手动更改。
origin_path:/data/YASDB_DATA/node_1/dbfiles/system # 数据库文件在原始数据库中的绝对路径，system为数据库文件，不可手动更改。
target_path:                                         # 指定该数据库文件的目标恢复路径，必须为绝对路径，且文件名必须和原始文件名一致。

tablespace_id:1, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/sysaux
target_path:

tablespace_id:2, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/temp
target_path:

tablespace_id:3, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/swap
target_path:

tablespace_id:4, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/users
target_path:/data/users

tablespace_id:5, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/undo
target_path:

tablespace_id:6, datafile_id:0
origin_path:/data/YASDB_DATA/node_1/dbfiles/tps_test_yasrman
target_path:

bucket_space_id:4, bucket_file_id:0
origin_path:/data/YASDB_DATA/node_1/local_fs/users
target_path:

bucket_space_id:6, bucket_file_id:1
origin_path:/data/YASDB_DATA/node_1/local_fs/tps_test_yasrman
target_path:
```

> **Note**：
>
> - 如果仅正确指定database_node_id对应的db_target_home（例如/data/NEW_YASDB_DATA）但不指定该节点下各tablespace_id对应的target_path，指定路径转换文件RESTORE时会将备份集恢复至/data/NEW_YASDB_DATA下的对应目录下。
> - 如果仅正确指定tablespace_id对应的target_path，则该表空间文件将恢复至单独指定的target_path中（例如，tablespace_id:4, datafile_id:0对应的target_path指定为/data/users，则其文件将恢复至/data/users）。
> - 如果db_target_home和tablespace_id对应的target_path同时指定，数据文件路径的优先级高于数据库指定的HOME路径。
> - CTRL文件和归档日志文件均恢复至原始路径，不参与路径转换。

