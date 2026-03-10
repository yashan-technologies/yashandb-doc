The LIST command is used to display the backup set information recorded in the *yasrman* repository.

## List Backup Set Information

```ebnf+diagram
syntax::= LIST BACKUP [TAG tag_name] [(DETAIL [FORMAT XML])|(NODE INFO TO "nodeinfo.txt")|(DATABASE CONFIGURE PARAMETER)]
```
View the backup set information across all nodes by specifying the backup set alias. When no TAG is specified, it will show all backup sets from all nodes.

### DETAIL

Specify DETAIL to list detailed information about the backup set, which is displayed in XML format by default.

### NODE INFO

List detailed deployment information of the backup nodes in ISC Distributed Cluster Deployment outputting to the specified temporary file nodeinfo.txt. The content is represented as follows:

```shell
node_id=1-1, node_type=MN, role=PRIMARY, endpoint=1, data_path=/data/shm/data/mn-1-1
node_id=2-1, node_type=CN, role=PRIMARY, endpoint=2, data_path=/data/shm/data/cn-2-1
node_id=3-1, node_type=DN, role=PRIMARY, endpoint=3, data_path=/data/shm/data/dn-3-1
node_id=4-1, node_type=DN, role=PRIMARY, endpoint=4, data_path=/data/shm/data/dn-4-1
node_id=5-1, node_type=DN, role=PRIMARY, endpoint=5, data_path=/data/shm/data/dn-5-1
```

### DATABASE CONFIGURE PARAMETER

List the backup information of the configuration files for the nodes in the backup set within the ISC Distributed Cluster Deployment, shown as follows:

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

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "list backup tag 'full_1'" -D /home/yashan/catalog
Group: type DATABASE, tag: full_1, format: /home/yashan/catalog/backup/full_bak_1, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 0
    backupset key: 3407244139, base key: 0, restore time: 2025-07-03 15:45:18.966827 (UTC+08:00), 
	backup path: /home/yashan/catalog/backup/full_bak_1 
    instance: 1, archive range sequence: 4-4 
                  scn: 711441947081232384-711442105212928000 

$ yasrman sys/********@192.168.1.2:1688 -c "list backup tag 'arch_all'" -D /home/yashan/catalog
Group: type ARCHIVE, tag: arch_all, format: /data/regress/ha_regress/ha_home/catalog/backup/arch_all, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: TRUE, offset: 24576
    backupset key: 408660232, base key: 0, restore time: 2025-07-03 15:45:18.966827 (UTC+08:00), 
	backup path: /data/regress/ha_regress/ha_home/catalog/backup/arch_all
    instance: 1, archive range sequence: 3-4 
                  scn: 711441943202398208-711442105212928000 
```

> **Note**: 
>
> The list command only lists the backup set metadata that exists in the specified catalog file. Since the catalog does not automatically synchronize the backup metadata with the database, the backup set metadata may not completely align with the contents of the DBA_BACKUP_SET view in the database.
> 
> If the DETAIL field is not specified, only summary information about the backup will be listed. If the DETAIL field is specified, the detailed contents of the backup set will be output in XML format by default.

Below is an example of the detailed backup set information output in XML format along with explanations of each field.
```shell
<?xml version="1.0" encoding="UTF-8"?>
<backupsets>
	<group>
		<backup_type>DB_INCR</backup_type> # Backup type, indicating a database incremental backup
		<backup_tag>bak_incr1</backup_tag> # Unique identifier for the backup set
		<backup_path>/home/yashan/backup/bak_incr1</backup_path>  # Backup set path
		<connect_url>192.168.1.2:1688</connect_url> # URL used to execute the backup
		<node_count>1</node_count> # Number of nodes in the backup set in ISC Distributed Cluster Deployment
		<is_distribution>FALSE</is_distribution> # Indicates if it is a database backup set for ISC Distributed Cluster Deployment
		<is_rman_side>FALSE</is_rman_side> # Indicates if the backup set is stored on the device where *yasrman* resides
		<dbinfo>
			<database_create_time>2025-07-03 23:11:04.750852 (UTC+08:00)</database_create_time> # Database creation time
			<database_id>3441381755</database_id> # Unique database ID
			<restore_time>NULL</restore_time> # Most recent restore time of the database, NULL indicates no restore has been executed
			<instance_count>1</instance_count> # Number of database instances
			<instance_map>1</instance_map> # Map of active instances during backup
		</dbinfo>
		<backupset>
			<start_time>2025-07-03 23:11:48.329668 (UTC+08:00)</start_time> # Backup start time
			<complete_time>2025-07-03 23:11:49.345991 (UTC+08:00)</complete_time> # Backup end time
			<backup_path>/home/yashan/backup/bak_incr1</backup_path> # Backup set path
			<base_backup_path>/home/yashan/backup/bak_incr0</base_backup_path> # For incremental backup set, this value indicates the baseline backup set path
			<base_lsn>6028</base_lsn> # Baseline lsn for the incremental backup set
			<backup_type>DB_INCR</backup_type> # Backup type
			<backup_level>1</backup_level> # Level of the incremental backup set
			<file_count>12</file_count> # Total number of files backed up
			<recover_begin>0-8-4-3275</recover_begin> # Start point of apply for the backup set
			<flush_point>0-8-4-3275</flush_point> # Minimum point that must be applied for the backup set
			<reset_point>0-0-0-0</reset_point> # Log point corresponding to resetlogs execution; 0 indicates no resetlogs operation was performed
			<trunc_lsn>6032</trunc_lsn> # Truncation lsn, indicating all data pages less than this lsn are backed up in the backup set
			<flush_lsn>6032</flush_lsn> # Flush lsn, indicating the lsn of the database after backing up the data file
			<consistence_scn>527444098809229312</consistence_scn> # Consistency scn, indicating that applying redo or archive log files to this scn ensures database consistency
			<base_lfn>3271</base_lfn> # Baseline lfn for the incremental backup set
			<encryption_algorithm>AES128</encryption_algorithm> # Encryption algorithm
			<input_bytes>411283456</input_bytes> # Total read IO size
			<output_bytes>16792576</output_bytes> # Total written IO size
			<backup_tag>bak_incr1</backup_tag> # Unique identifier for the backup set
			<thread_id>0</thread_id> # ID of the backup instance
			<is_base_tag_incr_bak>FALSE</is_base_tag_incr_bak> # Indicates if this is an incremental backup of a manually specified baseline backup set
			<instance>
				<instance_id>1</instance_id> # Instance ID
				<sequence_start>8</sequence_start> # ASN of the first archived backup file for this instance
				<sequence_end>8</sequence_end> # ASN of the last archived backup file for this instance
			</instance>
			<scn_start>527444076943417344</scn_start> # Minimum scn among all archives in the backup set
			<scn_end>527444098809229312</scn_end> # Maximum scn among all archives in the backup set
			<create_time>2025-07-03 23:11:04.750852 (UTC+08:00)</create_time> # Creation time of the target database for backup
			<database_version>571</database_version> # Version of the target database for backup
			<spc_import_scn>0</spc_import_scn> # If table space migration occurs, this SCN is updated to the maximum SCN of all new migrated tablespaces
			<spc_import_lsn>0</spc_import_lsn> # If table space migration occurs, this LSN is updated to the maximum LSN of all new migrated tablespaces
		</backupset>
	</group>
</backupsets>
```

<span id="LISTBACKUPSET" name="LISTBACKUPSET" class="yaslink"></span>

## List Backup Set File Path Information

```ebnf+diagram
syntax::= LIST BACKUPSET TAG tag_name MAPPED FILE mapfile_name
```
Use the list command to specify the backup set tag and list all data file path information corresponding to that tag in the mapfile.

The mapfile_name must be specified as an accessible absolute path.

The target_path option in the mapfile can be manually modified. Subsequently, when executing the RESTORE operation command, specify this mapfile to restore the corresponding data file to the appropriate target_path. Other contents, except for the target_path option, should not be manually modified, as doing so may lead to RESTORE operation failure or data anomalies.

An example of mapfile content is as follows:
```shell
database_node_id: 1-1  # Database node ID, used to determine the recovery path for the node in ISC Distributed Cluster Deployment; should not be manually changed.
db_origin_home:/data/YASDB_DATA/node_1  # Original database HOME path where the backup set was generated; should not be manually changed.
db_target_home:/data/NEW_YASDB_DATA  # Specify the target recovery path for the database files; must be specified at the folder level.
redo_target_path:/data/YASDB_DATA/rdhome  # Specify the recovery path for the database's REDO files.

tablespace_id:0, datafile_id:0  # Tablespace ID of the database file and its data file ID; should not be manually changed.
origin_path:/data/YASDB_DATA/node_1/dbfiles/system # Absolute path of the database file in the original database; system is the database file; should not be manually changed.
target_path:                                         # Specify the target recovery path for this database file; must be an absolute path, and the filename must match the original filename.

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
```

> **Note**: 
>
> - If only the db_target_home corresponding to the database_node_id is correctly specified (e.g., /data/NEW_YASDB_DATA) but the target_path for each tablespace_id under that node is not specified, the RESTORE will restore the backup set to the corresponding directory under /data/NEW_YASDB_DATA.
> - If only the target_path corresponding to the tablespace_id is correctly specified, the tablespace file will restore to the separately specified target_path (e.g., if target_path for tablespace_id:4, datafile_id:0 is specified as /data/users, this file will restore to /data/users).
> - If both db_target_home and the target_path corresponding to the tablespace_id are specified, the data file path has a higher priority than the specified HOME path of the database.
> - The CTRL files and archive log files will be restored to the original path and do not participate in path conversion.