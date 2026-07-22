The LIST command is used to display the backup set information recorded in the *yasrman* repository.

## List Backup Set Information

```ebnf
= LIST BACKUP [[(TAG tag_name)] [(DETAIL [FORMAT XML])|(NODE INFO TO "nodeinfo.txt")|(DATABASE CONFIGURE PARAMETER)] | (OF PLUGGABLE DATABASE pdb_name)].
```

View the backup set information across all nodes by specifying the backup set alias. When no TAG is specified, it will show all backup sets from all nodes.

### DETAIL

Specify DETAIL to list detailed information about the backup set, which is displayed in XML format by default.

### NODE INFO

List detailed deployment information of the standby nodes in ISC Distributed Cluster Deployment outputting to the specified temporary file nodeinfo.txt. The content is represented as follows:

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

### OF PLUGGABLE DATABASE

This statement is only applicable to CDBs and is used to list backup set information for specified PDBs. The full syntax is `LIST BACKUP OF PLUGGABLE DATABASE pdb_name`, and it cannot be used concurrently with other LIST commands.

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

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


# The following example command is applicable only to CDB
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

<span id="LISTBACKUPSET" name="LISTBACKUPSET"></span>

## Generate Path Mapping File

When restoring a database, by default, all files are restored to their original paths. If you need to adjust the storage location of data files or REDO log files — for example, after adding a high-performance disk, moving frequently accessed data files to the new disk — you can first prepare a path mapping file according to your planning, and then perform the restore operation based on this mapping file, at which point the corresponding data file(s) or REDO log files will be stored at the new `target_path`.

The main steps to generate a path mapping file include:

1. List the original paths of the data files in the target backup set into the path mapping file.

2. Edit the path mapping file and fill in the `target_path` (or possibly `xxx_target_path`) field to specify the new storage path for the target files.

### List Original Paths

```ebnf
= LIST BACKUPSET TAG tag_name [OF PLUGGABLE DATABASE pdb_name] MAPPED FILE mapfile_name.
```

This command is used to write the path information of all data files in the target backup set into the path mapping file.

#### TAG tag_name

Specifies the target backup set based on its tag; must be an existing `tag_name`.

#### OF PLUGGABLE DATABASE

Only applicable to a CDB. Used to specify the target PDB, indicating that only the path information of all data files belonging to this PDB in the target backup set will be written into the mapfile.

#### mapfile_name

Specifies the path and filename for the path mapping file, which must be an accessible absolute path.

### Configure Target Path

The content of the path mapping file generated by the `LIST BACKUPSET` command is shown as follows:

```text
database_node_id: 1-1   # Database node ID — cannot be manually modified.
db_origin_home:/data/yashan/yasdb_data/db-1-1  # The original DATA directory of the database recorded in the backup set — cannot be manually modified
db_target_home:   		# If you need to adjust the DATA directory, you can manually fill in this value to specify a path at the folder level.
redo_target_path:   	# If you need to adjust the recovery path for the database's REDO log files, you can manually fill in this value to specify a path at the folder level.

# In ISC Distributed Cluster Deployment, there is no subsequent content.

tablespace_id:0, datafile_id:0  	# Tablespace ID and file ID of the data file — cannot be manually modified.
origin_path:/data/yashan/yasdb_data/db-1-1/dbfiles/system		# The original path of the data file (filename + absolute path) recorded in the backup set — cannot be manually modified.
target_path:                        # If you need to adjust the recovery path for the data file, you can manually fill in this value, which must be specified as an absolute path and must match the original filename exactly.

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

When editing the path mapping file, you are only allowed to manually fill in the `target_path` (or possibly `xxx_target_path`) field, and you must not delete or add any row. The detailed path transformation rules are as follows:

-  Control files and archived log files are always restored to their original paths and are not subject to path transformation.

- When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), only the `db_target_home` and `redo_target_path` fields in the entire CDB's path mapping file are meaningful; the `target_path` field for data files is always ignored.

- In ISC Distributed Cluster Deployment, this file does not contain tablespace-related information — only the `db_target_home` and `redo_target_path` fields can be configured.

- In other scenarios, the `target_path` configuration for data files takes precedence over the `db_target_home` configuration of the database node. The path configuration and its precedence rules for the same database node are as follows:

	| db_target_home | `target_path` for the Data File |The Actual Recovery Path for the Data File |
	| -------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
	| Specified      | Specified                                                 | Restore to the corresponding `target_path`                   |
	| Not specified  | Specified                                                 | Restore to the corresponding `target_path`                   |
	| Specified      | Not specified                                             | Automatically create the `dbfiles` folder under `db_target_home` and restore to `{db_target_home}/dbfiles` |



