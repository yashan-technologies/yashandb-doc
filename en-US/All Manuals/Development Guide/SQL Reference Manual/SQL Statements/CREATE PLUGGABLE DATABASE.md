## General Description

CREATE PLUGGABLE DATABASE is used to create a pluggable database (PDB).



The applicable scope for this statement is as follows:

- This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE) and can only be executed when connected to the CDB root.

- This statement is not applicable to ISC Distributed Cluster Deployment.



Executing this statement requires the CDB root to be in OPEN state.

## Statement Definition

**create pluggable database::=**

```ebnf+diagram
syntax::= CREATE PLUGGABLE DATABASE pdb_name create_pdb_from_seed [COMPAT_MODE "=" (yashan|mysql)]
```

**[create_pdb_from_seed](#createpdbfromseed)**

```ebnf+diagram
syntax::= (file_name_convert|
ADMIN USER "admin_user_name" IDENTIFIED BY "password"|
default_tablespace|
(ARCHIVELOG|NOARCHIVELOG))
{" " (file_name_convert|
ADMIN USER "admin_user_name" IDENTIFIED BY "password"|
default_tablespace|
(ARCHIVELOG|NOARCHIVELOG))}
```

**[file_name_convert](#filenameconvert)**

```ebnf+diagram
syntax::= FILE_NAME_CONVERT "=" (NONE|"(" (("'" filename_pattern "'" "," "'" replacement_filename_pattern "'") {"," ("'" filename_pattern "'" "," "'" replacement_filename_pattern "'")}) ")")
```

**[default_tablespace](#defaulttablespace)**

```ebnf+diagram
syntax::= DEFAULT TABLESPACE (TEMPFILE|DATAFILE) datafiles_clause [extent_clause] [MEMORY MAPPED] [databucket_clause]
```

### 1. pdb\_name

Specifies the name of the PDB to be created, which cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="createpdbfromseed" name="createpdbfromseed" class="yaslink"></span>

### 2. create\_pdb\_from\_seed

This statement is used to create a standard PDB based on the PDB seed.

<span id="filenameconvert" name="filenameconvert" class="yaslink"></span>

#### 2.1. file\_name\_convert

This statement is used to configure the conversion rule for the data file path of a PDB (hereinafter simply referred to as "PDB_DATA"). The system will configure the PDB_DATA of the new PDB to the target path according to the conversion rule. It can be omitted, and when omitted, the default path will be used.


If you need to plan a custom data file path for a newly created PDB, you must first complete the creation of the corresponding path (and permission configuration) before creating the PDB, and use this statement to complete the path conversion when creating the PDB.

|Deployment Form  |Default PDB_DATA  |Requirements for Custom PDB_DATA  |
| ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| Standalone Deployment              | $YASDB_DATA/containers/{pdb_name} | Must be a local path, and the database installation user must have read-write permissions     |
| YAC/Distributed Cluster Deployment | +DG0/containers/{pdb_name}        |  Must be a [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) path    |

> **Note**:
>
> If you need to use the originally planned custom PDB_DATA when retrying after the PDB creation fails, you must also ensure that the target path is empty before retrying.



**NONE**

Indicates no conversion rules are specified, which is equivalent to omitting the file_name_convert clause.

**('filename_pattern','replacement_filename_pattern')**

- filename_pattern should be specified as the file path in the PDB seed. The PDB seed's files are all stored in the $YASDB_DATA/containers/PDB$SEED directory (Standalone Deployment) or +DG0/containers/PDB$SEED directory (YAC/Distributed Cluster Deployment).

- replacement_filename_pattern should specify a replacement pattern.

The above path specification format requirements are as follows:

- In Standalone Deployment, absolute paths (for example, `/data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/dbfiles`) or relative paths with `?` or `.` replacing $YASDB_DATA (for example, `?/containers/PDB$SEED/dbfiles` or `./containers/PDB$SEED/dbfiles`) can be used.

- In YAC/Distributed Cluster Deployment, it must be a complete YFS path (for example, `+DG0/containers/PDB$SEED/dbfiles`).

#### 2.2. ADMIN USER

This statement is used to specify the local user information of the PDB. It can be omitted, in which case no local users will be created.

If local users are created, they initially only have login privileges (CREATE SESSION).

<span id="defaulttablespace" name="defaulttablespace" class="yaslink"></span>

#### 2.3. default\_tablespace

This statement is used to specify the attributes of the built-in USERS tablespace (DEFAULT). It can be omitted, in which case the system will create the USERS tablespace with default values.  

The syntax rules are the same as [tablespace_clause](./CREATE DATABASE.html#tablespaceclause) in CREATE DATABASE.

#### 2.4. ARCHIVELOG|NOARCHIVELOG

This statement is used to specify whether the PDB enables archiving mode. If omitted, it follows the corresponding configuration of the CDB root.

### 3. COMPAT\_MODE

This statement is used to specify the syntax mode of PDB. It can be omitted, and if omitted, it defaults to yashan mode.

- yashan mode: Uses YashanDB's syntax system. After PDB creation, it cannot be switched to mysql mode. 

- [mysql mode](../../../Reference Manual of mysql Mode/Overview of mysql Mode): SQL statements input by users will be parsed according to MySQL's syntax system first, suitable for usage scenarios that require close adaptation to MySQL databases.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Specify conversion path  
CREATE PLUGGABLE DATABASE pdb1
FILE_NAME_CONVERT=('?/containers/PDB$SEED','?/pdb1')
ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1
DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Specify conversion path  
CREATE PLUGGABLE DATABASE pdb1
FILE_NAME_CONVERT=('+DG0/containers/PDB$SEED/dbfiles','+DG1/PDB1/dbfiles')
ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1
DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Do not specify conversion path  
CREATE PLUGGABLE DATABASE yashancdb_pdb2
FILE_NAME_CONVERT=NONE
ADMIN USER sys_pdb2 IDENTIFIED BY sys_pdb2
DEFAULT TABLESPACE DATAFILE 'userspdb2' size 128M;

-- Specify archive  
CREATE PLUGGABLE DATABASE yashancdb_pdb3
ADMIN USER sys_pdb3 IDENTIFIED BY sys_pdb3
DEFAULT TABLESPACE DATAFILE 'userspdb3' size 128M
ARCHIVELOG;

-- Specify syntax mode as mysql 
CREATE PLUGGABLE DATABASE yashancdb_pdb4
ADMIN USER sys_pdb4 IDENTIFIED BY sys_pdb4
DEFAULT TABLESPACE DATAFILE 'userspdb4' size 128M
ARCHIVELOG
COMPAT_MODE = mysql;
```
