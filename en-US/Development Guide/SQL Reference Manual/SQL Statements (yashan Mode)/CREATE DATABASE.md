## General Description

CREATE DATABASE is used to create a new database. This statement can only be executed in an empty instance that is in NOMOUNT state. After the system successfully creates a new database using this statement, it will also start it in OPEN state.

- In Standalone Deployment, the CREATE DATABASE statement creates a new single-instance database, and the database file path can only be specified as a local file system path.

- In ISC Distributed Cluster Deployment, the database is created based on the parameters specified in the configuration file during the *yasboot* installation process on all distributed nodes; subsequent execution of this statement on any node is not permitted (unless for fault recovery), as it may lead to node inconsistency in the distributed cluster.

- In YAC Deployment, a new multiple-instance database must be created using CREATE CLUSTER DATABASE, and all data files such as redo, undo, and swap must be specified as [YFS](../../../YashanDB for Cluster/Yashan File System/00Yashan File System) paths, otherwise an error will be returned.

During the database creation process, physical files will be created in the specified or default location. If any exceptions occur during the process that cause the database creation to fail, the already created files will remain, and the user will need to manually clean them up. If these files are not cleaned up and the same files are used again in a subsequent CREATE DATABASE, it will fail.

## Statement Definition

**create database::=**

```ebnf+diagram
syntax::= create_single_instance_database|create_multiple_instance_database
```

**[create_single_instance_database](#singleinstance)::=**

```ebnf+diagram
syntax::= CREATE DATABASE database_name [(attribute_clause) {" " (attribute_clause)}]
```

**[attribute\_clause](#attributeclause)::=**

```ebnf+diagram
syntax::=  (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  ((controlfile) {", " (controlfile)}) ")"| 
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
tablespace_clause)  
{" " (USER SYS IDENTIFIED BY password| 
CONTROLFILES "("  ((controlfile) {", " (controlfile)}) ")"| 
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
tablespace_clause)}
```

**[double\_write\_file\_clause](#doublewritefileclause)::=**

```ebnf+diagram
syntax::= DOUBLE_WRITE DATAFILE dbwfile SIZE size_clause
```

**[logfiles\_clause](#logfilesclause)::=**

```ebnf+diagram
syntax::= LOGFILE "(" (logfile SIZE size_clause [BLOCKSIZE size_clause]){"," (logfile SIZE size_clause [BLOCKSIZE size_clause])} ")"
```

**[tablespace\_clause](#tablespaceclause)::=**

```ebnf+diagram
syntax::= ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])  
{" " ((SYSTEM|SYSAUX|UNDO|SWAP|TEMPORARY|DEFAULT) TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause])}
```

**[datafiles\_clause](#datafilesclause)::=**

```ebnf+diagram
syntax::= (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]) 
{"," (datafile SIZE size_clause [AUTOEXTEND (ON|OFF)] [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)])} 
```

**[extent\_clause](#extentclause)::=**

```ebnf+diagram
syntax::= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause)
```

**[databucket\_clause](#databucketclause)::=**

```ebnf+diagram
syntax::= DATABUCKET (bucketurl) {"," (bucketurl)}
```

**[create_multiple_instance_database](#multipleinstance)::=**

```ebnf+diagram
syntax::= CREATE CLUSTER DATABASE database_name (INSTANCES integer | ([(attribute_clause) {" " (attribute_clause)}] (instance_clause) {" " (instance_clause)}) )
```

**[instance\_clause](#instanceclause)::=**

```ebnf+diagram
syntax::= INSTANCE "(" (logfiles_clause|UNDO TABLESPACE DATAFILE datafiles_clause)")"
```

<span id="singleinstance" name="singleinstance" class="yaslink"></span>

### 1. create\_single\_instance\_database

This statement is used to create a single-instance database.

#### 1.1. database\_name

Specifies the name of the database to be created, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="attributeclause" name="attributeclause" class="yaslink"></span>

#### 1.2. attribute\_clause

This statement is used to specify the configuration for creating the database.

##### 1.2.1. USER SYS IDENTIFIED BY

This statement is used to specify the password for the SYS user. It can be omitted; if so, the SYS user's password will remain the default value after the database is created, and it can be changed using the ALTER USER statement.

##### 1.2.2. CONTROLFILES

This statement is used to specify the control files created along with the database. Multiple control files are separated by `,`. It can be omitted, and the system will generate control files on disk based on the default values defined by the CONTROL_FILES parameter. YashanDB supports specifying a maximum of 8 control files.

##### 1.2.3. MAXDATAFILES

This statement is used to specify the maximum number of data files that the database can open. This value determines the initial space occupied by the Datafile Section in the control file. It can be omitted; the default value is 4096. YashanDB supports a maximum specified value of 16384.

##### 1.2.4. MAXDATABUCKETS

This statement is used to specify the maximum number of databuckets that the database can mount. It can be omitted; the default value is 256. YashanDB supports a maximum specified value of 4096.

<span id="maxinstances" name="maxinstances" class="yaslink"></span>

##### 1.2.5. MAXINSTANCES

This statement is used to specify the maximum number of instances that can simultaneously mount the database. It can be omitted; the default value is 8. YashanDB supports a maximum specified value of 64.

##### 1.2.6. CHARACTER SET

This statement is used to specify the character set used for storing data files in the database. It can be omitted; the default value is UTF8. YashanDB supports the character sets ASCII, GBK, UTF8, ISO8859-1, and GB18030.

If TAC tables and LSC tables are to be used, the database character set must be set to UTF8.

##### 1.2.7. NATIONAL CHARACTER SET 

This statement is used to specify the national character set used by the database. It can be omitted; the default value is UTF16. YashanDB only supports UTF16 as the national character set.

##### 1.2.8. SET TIME\_ZONE

This statement is used to specify the time zone used by the database. It can be omitted; the current operating system time zone will be taken. YashanDB supports a range of [-15:59~+15:59].

##### 1.2.9. UNDO\_SEGMENTS

This statement is used to specify the maximum number of rollback segments that can auto-extend in the database. It can be omitted; the default value is 64. YashanDB supports a maximum specified value of 1024.

##### 1.2.10. MAXLOGFILES

This statement is used to specify the reserved number of redo log file metadata. This value determines the initial space occupied by the Redofile Section in the control file. It can be omitted; the default value is 256, and YashanDB supports a maximum specified value of 4096. This value does not limit the number of redo log files that can be created by the instance; when the number of created redo logs exceeds this value, the control file may expand.

##### 1.2.11. MAXLOGHISTORY

This statement is used to specify the reserved number of archive log files metadata. This value determines the initial space occupied by the Archfile Section in the control file. It can be omitted; the default value is 64000, and YashanDB supports a maximum specified value of 1000000. This value does not limit the number of archive log files that can be created by the instance; when the number of created archive log files exceeds this value, the control file may expand.

##### 1.2.12. ARCHIVELOG|NOARCHIVELOG

This statement is used to specify whether the archiving mode is enabled for the database. It can be omitted; the default value is NOARCHIVELOG.

<span id="doublewritefileclause" name="doublewritefileclause" class="yaslink"></span>

##### 1.2.13. double\_write\_file\_clause

This statement is used to specify the path and size of the double-write file for the new database. It can be omitted; a file named dwf (Standalone Deployment) or double_write (ISC Distributed Cluster Deployment) will be created with a size of 8192 BLOCK in the system's default data file path.

In YAC Deployment, there is no double-write file.

DOUBLE_WRITE DATAFILE: keyword indicating the double-write file.

dbwfile: name of the double-write file, using the system's default data file path if not containing a path.

SIZE: size of the double-write file, with the minimum available size being influenced by configuration parameters DBWR_BUFFER_SIZE and DBWR_COUNT. The approximate minimum value is `4 * DBWR_BUFFER_SIZE * DBWR_COUNT + 4M`. If the specified size for creating the double-write file is too small, the database creation may fail.

<span id="logfilesclause" name="logfilesclause" class="yaslink"></span>

##### 1.2.14. logfiles\_clause

This statement is used to specify the path and size of the redo log files for the new database. It can be omitted; three files named redo1/redo2/redo3 will be created in the system's default data file path, each with a size of 128M and BLOCKSIZE of 4K.

LOGFILE: keyword indicating redo log files.

logfile: name of the log file, using the system's default data file path if not containing a path.

SIZE: size of the log file.

BLOCKSIZE: page size of the log file, defaulting to 4K, minimum 512 bytes, maximum 32K, typically set to the size of the operating system BLOCK. If this value is configured smaller than the system BLOCK, it may affect performance.

<span id="tablespaceclause" name="tablespaceclause" class="yaslink"></span>

##### 1.2.15. tablespace\_clause

When creating the database, built-in tablespaces will be created, including SYSTEM tablespace, SYSAUX tablespace, UNDO tablespace, SWAP tablespace, TEMP tablespace (TEMPORARY), USERS tablespace (DEFAULT), and USERS_AIM tablespace (only exists in ISC Distributed Cluster Deployment).

This statement is used to specify the attributes of the built-in tablespaces. It can be omitted, and the system will create built-in tablespaces according to the default values outlined below.

###### 1.2.15.1. TEMPFILE|DATAFILE

The data file type corresponding to the tablespace, where the SWAP tablespace and TEMP tablespace default to TEMPFILE, while other tablespaces default to DATAFILE.

<span id="datafilesclause" name="datafilesclause" class="yaslink"></span>

###### 1.2.15.2. datafiles\_clause

The name of the data file corresponding to the tablespace, with multiple data files separated by `,`. By default, a data file with the same name as the tablespace will be created in the system's default data file path (the DEFAULT tablespace defaults to a data file named users), with a size of 8192 BLOCK, auto-extend enabled (incrementing by 8192 BLOCK per extension), and a maximum extendable size of 64MB BLOCK (8MB BLOCK for UNDO tablespace).

<span id="extentclause" name="extentclause" class="yaslink"></span>

###### 1.2.15.3. extent\_clause

The space allocation method when requesting extents for the tablespace object. AUTOALLOCATE allocation method automatically assigns extent space according to the current size of the object, while UNIFORM allocation method assigns a fixed amount of extent space for each allocation.

- UNDO tablespaces and SWAP tablespaces do not allow the specification of extent allocation methods and can only use UNIFORM allocation method, defaulting to UNIFORM SIZE 1 BLOCK.

- TEMP tablespaces default to UNIFORM SIZE 8 BLOCK if no allocation method is specified.

- Other built-in tablespaces default to AUTOALLOCATE (system automatic allocation).

###### 1.2.15.4. MEMORY MAPPED

Specifies whether to enable data memory residency mode. It is disabled by default.

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

###### 1.2.15.5. databucket\_clause

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
UNDO TABLESPACE DATAFILE '?/dbfiles/undo' size 2G autoextend ON NEXT 256M MAXSIZE 64G
SWAP TABLESPACE TEMPFILE '?/dbfiles/swap' size 2G autoextend ON NEXT 256M MAXSIZE 64G
SYSTEM TABLESPACE DATAFILE 'system' size 128M MEMORY MAPPED
DEFAULT TABLESPACE DATAFILE '?/dbfiles/users' size 5G;
```

<span id="multipleinstance" name="multipleinstance" class="yaslink"></span>

### 2. create\_multiple\_instance\_database

This statement is used to create a multiple-instance database in YAC Deployment.

#### 2.1. database\_name

Specifies the name of the database to be created, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

#### 2.2. INSTANCES integer

This statement specifies the number of instances for the multiple-instance database in YAC Deployment, with a maximum of 4. It can be omitted; if omitted, the [instance_clause](#instanceclause) must be specified.

#### 2.3. attribute\_clause

Specifies the database configuration excluding redo and undo files. The attribute definitions are consistent with those in the create_single_instance_database attribute definition.

> **Note**: 
>
> In YAC Deployment, paths for redo, undo, swap, default, and all other data files must be specified as YFS paths, otherwise, an error will be returned.

<span id="instanceclause" name="instanceclause" class="yaslink"></span>

#### 2.4. instance\_clause

This statement is used to specify the configurations related to undo, redo log files, and data files for each instance of the multiple-instance database in YAC Deployment. This statement may be specified multiple times depending on the required number of instances, supporting a maximum of 4 (i.e., creating 4 instances), with statements separated by spaces.

***Example*** for YAC Deployment

```sql
--Create a database with 2 instances using the INSTANCES keyword, with default properties for the database files
CREATE cluster DATABASE yashan instances 2; 
--Create a database with 2 instances using instance_clause statement, with customized database file properties
CREATE cluster DATABASE yashan
SYSTEM TABLESPACE DATAFILE 'system' size 64M
SYSAUX TABLESPACE DATAFILE 'sysaux' size 64M
DEFAULT TABLESPACE DATAFILE 'users' size 64M
TEMPORARY TABLESPACE TEMPFILE 'temp' size 64M
SWAP TABLESPACE TEMPFILE 'swap' size 64M
instance(
LOGFILE('logfile11' size 32M, 'logfile12' size 32M, 'logfile13' size 32M)
UNDO TABLESPACE DATAFILE 'undo1' size 64M
)
instance (
LOGFILE('logfile21' size 32M, 'logfile22' size 32M, 'logfile23' size 32M)
UNDO TABLESPACE DATAFILE 'undo2' size 64M
); 
--Configuration with specified paths
CREATE cluster DATABASE yashan
instance (
LOGFILE('+DG_0/redo1' size 128M BLOCKSIZE 512,
'+DG_0/redo2' size 128M BLOCKSIZE 512)
UNDO TABLESPACE DATAFILE '+DG_0/undo1' size 128M autoextend ON)
SWAP TABLESPACE TEMPFILE '+DG_0/swap' size 128M autoextend ON
SYSAUX TABLESPACE DATAFILE '+DG_0/sysaux' size 128M autoextend ON
SYSTEM TABLESPACE DATAFILE '+DG_0/system' size 128M autoextend ON
TEMPORARY TABLESPACE TEMPFILE '+DG_0/temp' size 128M autoextend ON
DEFAULT TABLESPACE DATAFILE '+DG_0/users' size 128M autoextend ON;
```
