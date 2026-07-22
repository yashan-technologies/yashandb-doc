## General Description

CREATE DATABASE is used to create a new database. This statement can only be executed in an empty instance that is in NOMOUNT state. After the system successfully creates a new database using this statement, it will also start it in OPEN state.

- In Standalone Deployment, the CREATE DATABASE statement creates a new single-instance database, and the database file path can only be specified as a local file system path.

- In YAC/Distributed Cluster Deployment, a new multiple-instance database must be created using CREATE CLUSTER DATABASE, and all data files such as redo, undo, and swap must be specified as [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) paths, otherwise an error will be returned.

- In ISC Distributed Cluster Deployment, the database is created based on the parameters specified in the configuration file during the *yasboot* installation process on all distributed nodes; subsequent execution of this statement on any node is not permitted (unless for fault recovery), as it may lead to node inconsistency in the distributed cluster.

During the database creation process, physical files will be created in the specified or default location. If any exceptions occur during the process that cause the database creation to fail, the already created files will remain, and the user will need to manually clean them up. If these files are not cleaned up and the same files are used again in a subsequent CREATE DATABASE, it will fail.

## Statement Definition

**create database::=**

```ebnf
= create_single_instance_database|create_multiple_instance_database.
```

**[create_single_instance_database](#singleinstance)::=**

```ebnf
= CREATE DATABASE database_name [attribute_clause {" " attribute_clause}].
```

**[attribute_clause](#attributeclause)::=**

```ebnf
=  (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  (controlfile {", " controlfile}) ")"| 
MAXDATAFILES integer| 
MAXDATABUCKETS integer| 
MAXINSTANCES integer| 
CHARACTER SET charset|
NATIONAL CHARACTER SET charset|
SET TIME_ZONE|
UNDO_SEGMENTS integer| 
MAXLOGFILES  integer| 
MAXLOGHISTORY integer|     
(ARCHIVELOG|NOARCHIVELOG)| 
double_write_file_clause|  
logfiles_clause|
tablespace_clause|
enable_pluggable_database|
IN MEMORY)  
{" " (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  (controlfile {", " controlfile}) ")"| 
MAXDATAFILES integer| 
MAXDATABUCKETS integer| 
MAXINSTANCES integer| 
CHARACTER SET charset|
NATIONAL CHARACTER SET charset|
SET TIME_ZONE|
UNDO_SEGMENTS integer| 
MAXLOGFILES  integer| 
MAXLOGHISTORY integer| 
(ARCHIVELOG|NOARCHIVELOG)| 
double_write_file_clause| 
logfiles_clause|
tablespace_clause|
enable_pluggable_database|
IN MEMORY)}.
```

**[double_write_file_clause](#doublewritefileclause)::=**

```ebnf
= DOUBLE_WRITE DATAFILE dbwfile SIZE size_clause.
```

**[logfiles_clause](#logfilesclause)::=**

```ebnf
= LOGFILE "(" (logfile SIZE size_clause [BLOCKSIZE size_clause]){"," (logfile SIZE size_clause [BLOCKSIZE size_clause])} ")".
```

**[tablespace_clause](#tablespaceclause)::=**

```ebnf
= ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}.
```

**[datafiles_clause](#datafilesclause)::=**

```ebnf
= (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]) 
{"," (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)])} .
```

**[extent_clause](#extentclause)::=**

```ebnf
= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause).
```

**[databucket_clause](#databucketclause)::=**

```ebnf
= DATABUCKET bucketurl {"," bucketurl}.
```

**[enable_pluggable_database](#enablepluggabledatabase)**

```ebnf
= ENABLE PLUGGABLE DATABASE [SEED 
((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}].
```

**[create_multiple_instance_database](#multipleinstance)::=**

```ebnf
= CREATE CLUSTER DATABASE database_name (INSTANCES integer | ([attribute_clause {" " attribute_clause}] instance_clause {" " instance_clause}) ).
```

**[instance_clause](#instanceclause)::=**

```ebnf
= INSTANCE "(" (logfiles_clause|UNDO TABLESPACE DATAFILE datafiles_clause)")".
```

<span id="singleinstance" name="singleinstance"></span>

### create_single_instance_database

This statement is used to create a single-instance database.

#### database_name

Specifies the name of the database to be created, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="attributeclause" name="attributeclause"></span>

#### attribute_clause

This statement is used to specify the configuration for creating the database.

##### USER SYS IDENTIFIED BY

This statement is used to specify the password for the SYS user. It can be omitted; if so, the SYS user's password will remain the default value after the database is created, and it can be changed using the ALTER USER statement.

##### CONTROLFILES

This statement is used to specify the control files created along with the database. Multiple control files are separated by `,`. It can be omitted, and the system will generate control files on disk based on the default values defined by the CONTROL_FILES parameter. YashanDB supports specifying a maximum of 8 control files.

##### MAXDATAFILES

This statement is used to specify the maximum number of data files that the database can open. This value determines the initial space occupied by the Datafile Section in the control file. It can be omitted; the default value is 4096. YashanDB supports a maximum specified value of 16384.

##### MAXDATABUCKETS

This statement is used to specify the maximum number of databuckets that the database can mount. It can be omitted; the default value is 256. YashanDB supports a maximum specified value of 4096.

<span id="maxinstances" name="maxinstances"></span>

##### MAXINSTANCES

This statement is used to specify the maximum number of instances that can simultaneously mount the database. It can be omitted; the default value is 8. YashanDB supports a maximum specified value of 64.

This statement is used to specify the maximum number of instances supported by a standalone database, or the maximum number of nodes during online expansion of a YAC database. In version 23.4, standalone databases only support single-instance deployment, with multi-instance capabilities to be introduced in subsequent versions.

##### CHARACTER SET

This statement is used to specify the character set used for storing data files in the database. It can be omitted; the default value is UTF8. YashanDB supports the character sets ASCII, GBK, UTF8, ISO8859-1, and GB18030.

If TAC tables and LSC tables are to be used, the database character set must be set to UTF8.

##### NATIONAL CHARACTER SET 

This statement is used to specify the national character set used by the database. It can be omitted; the default value is UTF16. YashanDB only supports UTF16 as the national character set.

##### SET TIME_ZONE

This statement is used to specify the time zone used by the database. It can be omitted; the current operating system time zone will be taken. YashanDB supports a range of [-15:59~+15:59].

##### UNDO_SEGMENTS

This statement is used to specify the maximum number of rollback segments that can auto-extend in the database. It can be omitted; the default value is 64. YashanDB supports a maximum specified value of 1024.

##### MAXLOGFILES

This statement is used to specify the reserved number of redo log file metadata. This value determines the initial space occupied by the Redofile Section in the control file. It can be omitted; the default value is 256, and YashanDB supports a maximum specified value of 4096. This value does not limit the number of redo log files that can be created by the instance; when the number of created redo logs exceeds this value, the control file may expand.

##### MAXLOGHISTORY

This statement is used to specify the reserved number of archive log files metadata. This value determines the initial space occupied by the Archfile Section in the control file. It can be omitted; the default value is 64000, and YashanDB supports a maximum specified value of 1000000. This value does not limit the number of archive log files that can be created by the instance; when the number of created archive log files exceeds this value, the control file may expand.

##### ARCHIVELOG|NOARCHIVELOG

This statement is used to specify whether the archiving mode is enabled for the database. It can be omitted; the default value is NOARCHIVELOG.

<span id="doublewritefileclause" name="doublewritefileclause"></span>

##### double_write_file_clause

This statement is used to specify the path and size of the double-write file for the new database. It can be omitted; a file named dwf (Standalone Deployment) or double_write (ISC Distributed Cluster Deployment) will be created with a size of 8192 BLOCK in the system's default data file path.

In YAC/Distributed Cluster Deployment, there is no double-write file.

DOUBLE_WRITE DATAFILE: keyword indicating the double-write file.

dbwfile: name of the double-write file, using the system's default data file path if not containing a path.

SIZE: size of the double-write file, with the minimum available size being influenced by configuration parameters DBWR_BUFFER_SIZE and DBWR_COUNT. The approximate minimum value is `4 * DBWR_BUFFER_SIZE * DBWR_COUNT + 4M`. If the specified size for creating the double-write file is too small, the database creation may fail.

<span id="logfilesclause" name="logfilesclause"></span>

##### logfiles_clause

This statement is used to specify the path and size of the redo log files for the new database. It can be omitted; three files named redo1/redo2/redo3 will be created in the system's default data file path, each with a size of 128M and BLOCKSIZE of 4K.

LOGFILE: keyword indicating redo log files.

logfile: name of the log file, using the system's default data file path if not containing a path.

SIZE: size of the log file.

BLOCKSIZE: page size of the log file, defaulting to 4K, minimum 512 bytes, maximum 32K, typically set to the size of the operating system BLOCK. If this value is configured smaller than the system BLOCK, it may affect performance.

<span id="tablespaceclause" name="tablespaceclause"></span>

##### tablespace_clause

When creating the database, built-in tablespaces will be created, including SYSTEM tablespace, SYSAUX tablespace, UNDO tablespace, SWAP tablespace, TEMP tablespace (TEMPORARY), USERS tablespace (DEFAULT), and USERS_AIM tablespace (only exists in ISC Distributed Cluster Deployment).

This statement is used to specify the attributes of the built-in tablespaces. It can be omitted, and the system will create built-in tablespaces according to the default values outlined below.

###### TEMPFILE|DATAFILE

The data file type corresponding to the tablespace, where the SWAP tablespace and TEMP tablespace default to TEMPFILE, while other tablespaces default to DATAFILE.

<span id="datafilesclause" name="datafilesclause"></span>

###### datafiles_clause

The name of the data file corresponding to the tablespace, with multiple data files separated by `,`. By default, a data file with the same name as the tablespace will be created in the system's default data file path (the DEFAULT tablespace defaults to a data file named users), with a size of 8192 BLOCK, auto-extend enabled (incrementing by 8192 BLOCK per extension), and a maximum extendable size of 64MB BLOCK (8MB BLOCK for UNDO tablespace).

<span id="extentclause" name="extentclause"></span>

###### extent_clause

The space allocation method when requesting extents for the tablespace object. AUTOALLOCATE allocation method automatically assigns extent space according to the current size of the object, while UNIFORM allocation method assigns a fixed amount of extent space for each allocation.

- UNDO tablespaces and SWAP tablespaces do not allow the specification of extent allocation methods and can only use UNIFORM allocation method, defaulting to UNIFORM SIZE 1 BLOCK.

- TEMP tablespaces default to UNIFORM SIZE 8 BLOCK if no allocation method is specified.

- Other built-in tablespaces default to AUTOALLOCATE (system automatic allocation).

###### MEMORY MAPPED

Specifies whether to enable data memory residency mode. It is disabled by default.

<span id="databucketclause" name="databucketclause"></span>

###### databucket_clause

If DATABUCKET is specified for a tablespace, the system will create a directory for object storage according to the specified path information. For the DEFAULT tablespace, if DATABUCKET is not specified, the system will create a users folder for object storage in the default path (local_fs).

***Example*** for Standalone Deployment

```sql
CREATE DATABASE yashan
CHARACTER SET utf8
NATIONAL CHARACTER SET utf16
ARCHIVELOG
LOGFILE ('/home/yasdb/YASDB_DATA/dbfiles/redo1' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo2' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo3' size 2G BLOCKSIZE 512,
'/home/yasdb/YASDB_DATA/dbfiles/redo4' size 2G BLOCKSIZE 512)
UNDO TABLESPACE DATAFILE '?/dbfiles/undo' size 2G autoextend on next 256M maxsize 64G
SWAP TABLESPACE TEMPFILE '?/dbfiles/swap' size 2G autoextend on next 256M maxsize 64G
SYSTEM TABLESPACE DATAFILE 'system' size 128M MEMORY MAPPED
DEFAULT TABLESPACE DATAFILE '?/dbfiles/users' size 5G;
```

<span id="enablepluggabledatabase" name="enablepluggabledatabase"></span>

##### enable_pluggable_database

This statement is used to create a Container Database (CDB). Each CDB contains two built-in containers by default after creation:  


- CDB root: The current instance will be converted to the CDB root, with the default name `CDB$ROOT`. The CDB root itself is a fully functional database instance with independent data files and transaction systems, but its core responsibility is to manage the entire CDB's metadata, global users, resource plans, and PDB lifecycle. All container management operations (such as creating and deleting PDBs) must be executed in the CDB root.  

- PDB seed: The system will automatically create a read-only PDB named PDB$SEED as the standard template for new PDBs, which is default in closed state. Its configuration is defined by the SEED clause, and undefined or undefinable parts of the configuration will default to inheriting from the CDB root, such as character set. Subsequent PDB creation (CREATE PLUGGABLE DATABASE) is all based on cloning PDB$SEED, including system tablespace structure, initial users, permission configuration, etc.


The following rules must be followed when creating a CDB:

- Only applicable to Standalone (Primary-Standby) Deployment, YAC Deployments (cannot be primary-standby YAC) or Distributed Cluster Deployment.  

- Ensure that the CDB feature is enabled (i.e., configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE). The default value is FALSE.  

- The enable_pluggable_database clause has no enforced order relationship with other clauses/keyword attributes.

- To define attributes for PDB seed, you should continuously specify all PDB seed attributes after the SEED keyword. Especially for built-in tablespaces (SYSTEM, SYSAUX, UNDO, SWAP, or TEMPORARY), if you specify attributes for both PDB seed and CDB root's built-in tablespaces (i.e., the same built-in tablespace attribute clause appears **twice** in the statement), the following rules apply: The second occurrence of any built-in tablespace attribute (and all subsequent tablespace definitions) will be treated as CDB root's attributes.

###### SEED tablespace_clause

When creating a PDB seed, built-in tablespaces are also created, including SYSTEM tablespace, SYSAUX tablespace, UNDO tablespace, SWAP tablespace, and TEMP tablespace (TEMPORARY).  

This statement is used to specify the attributes of the built-in tablespaces in the PDB seed. The syntax rules are the same as [tablespace_clause](#tablespaceclause).  

***Example*** for Standalone Deployment

```sql
-- Do not specify PDB seed attributes
CREATE DATABASE yashancdb ARCHIVELOG ENABLE PLUGGABLE DATABASE;

-- Specify partial attributes for PDB seed
CREATE DATABASE yashancdb
	SYSTEM TABLESPACE DATAFILE 'system' size 500M
	SYSAUX TABLESPACE DATAFILE 'sysaux' size 500M
	DEFAULT TABLESPACE DATAFILE 'usersroot' size 500M
	ARCHIVELOG
	ENABLE PLUGGABLE DATABASE
		SEED
			SYSTEM TABLESPACE DATAFILE 'system' size 128M
			SYSAUX TABLESPACE DATAFILE 'sysaux' size 128M;
```

##### IN MEMORY

YashanDB now supports trial use of distributed in-memory databases. Deployment as a distributed in-memory database can be specified during installation, but creating distributed in-memory database instances through this statement is not permitted. This statement is only provided for creating standalone in-memory database instances and has no practical effect.

<span id="multipleinstance" name="multipleinstance"></span>

### create_multiple_instance_database

This statement is used to create a multiple-instance database in YAC/Distributed Cluster Deployment.

#### database_name

Specifies the name of the database to be created, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

#### INSTANCES integer

This statement specifies the number of instances for the multiple-instance database in YAC/Distributed Cluster Deployment, with a maximum of 4. It can be omitted; if omitted, the [instance_clause](#instanceclause) must be specified.

#### attribute_clause

Specifies the database configuration excluding redo and undo files. The attribute definitions are consistent with those in the create_single_instance_database attribute definition.

> **Note**: 
>
> In YAC/Distributed Cluster Deployment, paths for redo, undo, swap, default, and all other data files must be specified as YFS paths, otherwise, an error will be returned.

<span id="instanceclause" name="instanceclause"></span>

#### instance_clause

This statement is used to specify the configurations related to undo, redo log files, and data files for each instance of the multiple-instance database in YAC/Distributed Cluster Deployment. This statement may be specified multiple times depending on the required number of instances, supporting a maximum of 4 (i.e., creating 4 instances), with statements separated by spaces.

When creating a CDB in YAC/Distributed Cluster Deployment, the number of `instance` clauses specified for both the CDB root and the PDB seed must match the actual number of instances.

***Example*** for YAC/Distributed Cluster Deployment

```sql
-- Create a database with 2 instances using the INSTANCES keyword, with default properties for the database files
CREATE CLUSTER DATABASE yashan instances 2; 
-- Create a database with 2 instances using instance_clause statement, with customized database file properties
CREATE CLUSTER DATABASE yashan
system tablespace datafile 'system' size 64M
sysaux tablespace datafile 'sysaux' size 64M
default tablespace datafile 'users' size 64M
temporary tablespace tempfile 'temp' size 64M
swap tablespace tempfile 'swap' size 64M
instance(
logfile('logfile11' size 32M, 'logfile12' size 32M, 'logfile13' size 32M)
undo tablespace datafile 'undo1' size 64M
)
instance (
logfile('logfile21' size 32M, 'logfile22' size 32M, 'logfile23' size 32M)
undo tablespace datafile 'undo2' size 64M
); 

-- Create a CDB with 2 instances using the INSTANCES keyword, with default properties for the database files
CREATE CLUSTER DATABASE yashancdb INSTANCES 2 ENABLE PLUGGABLE DATABASE;

-- Create a CDB with 2 instances using instance_clause statement, with customized database file properties
create cluster database yashancdb2         
	instance (
        logfile('+DG0/dbfiles/redo1' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo2' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo3' size 256M BLOCKSIZE 512)
        UNDO TABLESPACE DATAFILE '+DG0/dbfiles/undo1' size 128M autoextend on
    )
    instance (
        logfile('+DG0/dbfiles/redo4' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo5' size 256M BLOCKSIZE 512,
        '+DG0/dbfiles/redo6' size 256M BLOCKSIZE 512)
        UNDO TABLESPACE DATAFILE '+DG0/dbfiles/undo2' size 128M autoextend on
    )
    SYSTEM TABLESPACE DATAFILE '+DG0/dbfiles/system' size 128M autoextend on
    SWAP TABLESPACE TEMPFILE '+DG0/dbfiles/swap' size 128M autoextend on
    DEFAULT TABLESPACE DATAFILE '+DG0/dbfiles/users' size 128M autoextend on    
    SYSAUX TABLESPACE DATAFILE '+DG0/dbfiles/sysaux' size 128M autoextend on
    TEMPORARY TABLESPACE TEMPFILE '+DG0/dbfiles/temp' size 128M autoextend on
    ARCHIVELOG
    ENABLE PLUGGABLE DATABASE
        SEED 
            SYSTEM TABLESPACE DATAFILE 'system' size 128M
            SYSAUX TABLESPACE DATAFILE 'sysaux' size 128M    
            SWAP TABLESPACE TEMPFILE 'swap' size 128M
            TEMPORARY TABLESPACE TEMPFILE 'temp' size 128M   
            instance (
                UNDO TABLESPACE DATAFILE '+DG0/containers/PDB$SEED/dbfiles/undo1' size 128M autoextend on
                logfile('+DG0/containers/PDB$SEED/dbfiles/redo1' size 128M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo2' size 256M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo3' size 512M BLOCKSIZE 512
                )
            )
            instance (
                UNDO TABLESPACE DATAFILE '+DG0/containers/PDB$SEED/dbfiles/undo2' size 128M autoextend on
                logfile('+DG0/containers/PDB$SEED/dbfiles/redo4' size 128M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo5' size 256M BLOCKSIZE 512,
                        '+DG0/containers/PDB$SEED/dbfiles/redo6' size 512M BLOCKSIZE 512
                )
            )        
    ;
```
