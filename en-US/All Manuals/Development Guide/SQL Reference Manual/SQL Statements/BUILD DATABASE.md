## General Description

BUILD DATABASE is used to create a standby database on the standby database in a primary/standby high availability deployment environment. For detailed operational descriptions of the primary/standby high availability architecture, please refer to [High Availability](../../../High Availability/Overview of YashanDB High Availability).

In the presence of a cascade backup environment, the upper-level standby database of the cascade standby database corresponds to the primary database mentioned below, while the cascade standby database corresponds to the standby database mentioned below, having the same syntax, functionality, and constraints as the primary-standby database.

The BUILD command on the primary database can only be executed by the SYS superuser or users with SYSDBA or SYSBACKUP privileges. Before executing this statement, the current node's protection mode must be checked using the V$DATABASE view. If the PROTECTION_MODE field is set to MAXIMIZE PROTECTION mode, it is necessary to [switch](ALTER DATABASE.html#set_standby_clause) to another mode before initiating the BUILD. After BUILD completion, you can switch back to maximize protection mode as needed.

In Standalone Deployment and YAC Deployment, when executing the BUILD operation, the system will first perform an automatic environmental pre-check. The checks include whether the file path to be restored is accessible and whether there is enough free disk space at the target address. The BUILD will only be executed if the checks are passed; if the checks fail, the process will exit directly.

This functionality is not applicable to ISC Distributed Cluster Deployment.

The prerequisites for successfully executing this statement on the standby database are:

* The primary database is OPEN and in archiving mode.
* The standby database has the database software installed and the instance started to NOMOUNT state (the standby database in incremental BUILD must be in OPEN state).
* The network and privileges on the primary-standby database are clear, and relevant configuration parameter values are correctly set.
* All constraints of the [CREATE DATABASE](CREATE DATABASE) statement are met.

This statement covers various scenarios for YashanDB that require BUILD operations:

- **Full BUILD**: Used for initialization when creating or rebuilding the standby database.

  - Initiated by the standby database itself for BUILD ([BUILD DATABASE](#builddatabase)): Suitable for standalone, YAC Deployment.
  - Initiated from the primary database to the standby database for BUILD ([BUILD DATABASE TO STANDBY](#builddatabasetostandby) to specify a single standby_name or [BUILD DATABASE TO REMOTE](#builddatabasetoremote) to specify a single IP): Suitable for standalone, YAC Deployment.
  - Initiated from the primary database to multiple standby databases simultaneously for BUILD ([BUILD DATABASE TO STANDBY](#builddatabasetostandby) or [BUILD DATABASE TO REMOTE](#builddatabasetoremote)): Suitable for Standalone Deployment.
  - Initiated by the standby database to multiple standby databases simultaneously for BUILD ([BUILD DATABASE TO REMOTE](#builddatabasetoremote)): Suitable for Standalone Deployment.

- **Incremental BUILD**: Used for quick recovery of abnormal standby databases.

  - Initiated from the primary database to a standby database with a gap for BUILD ([BUILD DATABASE INCREMENTAL TO STANDBY](#builddatabaseincstandby) or [BUILD DATABASE INCREMENTAL TO REMOTE](#builddatabasetoremote)): Suitable for Standalone Deployment.
  - Initiated by the standby database to another standby database with a gap for BUILD ([BUILD DATABASE INCREMENTAL TO REMOTE](#builddatabasetoremote)): Suitable for Standalone Deployment.
  - Initiated from the primary database to a standby database that needs repair for BUILD ([BUILD DATABASE REPAIR STANDBY](#builddatabaserepstandby)): Suitable for Standalone Deployment.

During the execution of this statement, the progress of a particular BUILD operation can be queried through the V$BACKUP_PROGRESS view. If the BUILD is interrupted or fails, please note:

-  During the execution of this statement, the system will transfer the control files, data files, redo log files, and archive log files of the primary database (or a standby database) online to the corresponding directory of the standby database. If an error occurs during this process, an error will be reported, and the operation will be interrupted. Transferred files will not be automatically cleared; before rerunning this statement, you need to manually clear the dirty data on the standby database.
- For parallel BUILD operations initiated for multiple standby databases, if one standby database BUILD operation fails, all standby database BUILD operations will fail.

## Statement Definition

**build database::=**

```ebnf+diagram
syntax::= BUILD DATABASE (((INCREMENTAL TO ((STANDBY standby_name) | (REMOTE standby_address))) | (REPAIR STANDBY standby_name) | (TO ((STANDBY "(" (((standby_name) {"," (standby_name)})| "*")")") | (REMOTE "(" (standby_address) {"," (standby_address)}")")) [SKIP VALIDATE] [OVERWRITE] )) [PARALLELISM integer] [DISCONNECT FROM SESSION]|[SKIP VALIDATE] [OVERWRITE] [PARALLELISM integer])
```

<span id="builddatabase" name="builddatabase" class="yaslink"></span>

### 1. BUILD DATABASE

When there are no options after this statement, it indicates that this operation is executed on the standby database, transferring files from the primary database server to the standby database server online.

### 2. SKIP VALIDATE

Skip the disk check during the build process. By default, data build checks are performed during the build process, including checks for the restoration target path of the standby database file, whether there is enough disk space at the restoration target, and whether files with the same name already exist.

### 3. OVERWRITE

If a file with the same name exists at the restoration target during the build process, that file will be deleted, and a new file with the same name will be created at the restoration target path. REDO files and archive files cannot be overwritten using this parameter; if a file with the same name exists, please clear it manually.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Execute on a specific standby database
BUILD DATABASE;
```

<span id="builddatabasetostandby" name="builddatabasetostandby" class="yaslink"></span>

### 4. BUILD DATABASE TO STANDBY

This operation is generally executed on the primary database, used to specify multiple different standby databases, transmitting files from the primary database server to these standby database servers simultaneously.

In YAC Deployment, only one backup cluster can be specified when executing this operation.

<span id="standbyname" name="standbyname" class="yaslink"></span>

**standby_name**

Specifies the DB_UNIQUE_NAME of the standby database (obtained via the V$ARCHIVE_DEST view). In an HA cluster, DB_UNIQUE_NAME uniquely identifies a standby database server.

Multiple standby_names are separated by `,` and must not be repeated.

**\***

Specifies all standby databases connected to this primary database.

***Example*** for Standalone Deployment

```sql
-- Execute BUILD for the specified corresponding standby database DB_UNIQUE_NAME.
BUILD DATABASE TO STANDBY (standby1_name, standby2_name);

-- Specifying * means executing BUILD for all standby databases of this primary database.
BUILD DATABASE TO STANDBY (*);
```

***Example*** for YAC Deployment

```sql
-- Execute BUILD for the specified corresponding backup cluster DB_UNIQUE_NAME.
BUILD DATABASE TO STANDBY (standby1_name);
```

<span id="builddatabasetoremote" name="builddatabasetoremote" class="yaslink"></span>

### 5. BUILD DATABASE TO REMOTE

This operation can be executed on either the primary database or the standby database, used to specify multiple different IPs, transferring files from the local server to these servers simultaneously.

When a standby database encounters an anomaly, to avoid impacting the primary database's operations, this statement can be executed on a normally synchronized standby database, specifying the IP address to transfer server files to the anomalous standby database for repair.

In YAC Deployment, only one backup cluster can be specified when executing this operation.

<span id="standbyaddress" name="standbyaddress" class="yaslink"></span>

**standby_address**

Specifies the IP address of the standby database, which must be a valid address in the `address:port` format, such as `127.0.0.1:1689`.

Multiple standby_addresses are specified using `,` and must not be repeated.

YashanDB supports specifying an IP address that has not established a replication link with the primary database. In this case, the primary database can still connect to the standby database and perform BUILD operations (although no links have been established, the standby database is only creating a standby library and not joining the HA cluster). However, it must adhere to all restrictions of the HA cluster environment of the primary database, such as the number of specified IPs not exceeding the maximum limit of standby databases.

***Example*** for Standalone Deployment

```sql
BUILD DATABASE TO REMOTE ('127.0.0.1:2901', '127.0.0.1:2902');
```

***Example*** for YAC Deployment

```sql
BUILD DATABASE TO REMOTE ('127.0.0.1:2901');
```

<span id="builddatabaseincstandby" name="builddatabaseincstandby" class="yaslink"></span>

### 6. BUILD DATABASE INCREMENTAL TO STANDBY

When there is a large gap between the standby database and the primary database, this statement can be executed on the primary database by specifying the [standby_name](#standbyname) for quick recovery of the abnormal standby database and reducing the gap between primary/standby.

This operation can only specify one standby database at a time, and the specified standby database must be in a normal open state (the state of the standby database can be checked via the V$REPLICATION_STATUS view).

> **Caution**: 
>
> - This operation will automatically restart the standby database, clear the control file, and during the BUILD process, the standby database will be in a non-open state, so use it with caution.
> - If the operation fails midway, only a full BUILD repair can be performed on the standby database.

***Example*** for Standalone Deployment
```sql
BUILD DATABASE INCREMENTAL TO STANDBY standby1_name;
```

<span id="builddatabaseincstandby" name="builddatabaseincstandby" class="yaslink"></span>

### 7. BUILD DATABASE INCREMENTAL TO REMOTE

When there is a large gap between the standby database and the primary database, this statement can be executed on the primary database by specifying the [standby_address](#standbyaddress) for quick recovery of the abnormal standby database and reducing the gap between primary/standby.

To avoid impacting the primary database's operations, this statement can also be executed on a normal synchronized standby database or a standby database that has a smaller gap with the primary.

This operation can only specify one IP address at a time (specified through [standby_address](#standbyaddress)), and the specified standby database must be in a normal open state (the state of the standby database can be checked via the V$REPLICATION_STATUS view).

> **Caution**: 
>
> - This operation will automatically restart the standby database, clear the control file, and during the BUILD process, the standby database will be in a non-open state, so use it with caution.
> - If the operation fails midway, only a full BUILD repair can be performed on the standby database.

***Example*** for Standalone Deployment
```sql
BUILD DATABASE INCREMENTAL TO REMOTE '127.0.0.1:1689';
```

<span id="builddatabaserepstandby" name="builddatabaserepstandby" class="yaslink"></span>

### 8. BUILD DATABASE REPAIR STANDBY

When a standby database is in a need repair state (can be checked via the V$REPLICATION_STATUS view), this statement can be executed on the primary database by specifying the [standby_name](#standbyname) to quickly repair the abnormal standby database.

This operation can only specify one standby database at a time, and the specified standby database must be in a need repair state due to certain abnormal types (which can be checked via the V$REPLICATION_STATUS view), such as the primary database archives being purged. Other anomalies can only be repaired through a full BUILD. For more detailed descriptions, please refer to the high availability manual on [Repairing Abnormal Standby Databases](../../../High Availability/Standby Repair).

> **Note**: 
>
> - If the primary database only performs backup and recovery operations, it can also cause a split-brain phenomenon. In this case, a complete BUILD is required to rebuild the standby database, and the split-brain repair operation cannot be used to repair the standby database.

***Example*** for Standalone Deployment
```sql
BUILD DATABASE REPAIR STANDBY standby1_name;
```

### 9. PARALLELISM integer

Specifies the level of parallelism for the build database operation, with integer values ranging from `[1,16]`, with a default parallelism level of 4.

### 10. DISCONNECT FROM SESSION

This option indicates that there is no need to wait for the BUILD operation to complete before returning success. In this case, the BUILD operation is completed by a background thread, and the user's session thread will not be blocked. If this option is not specified, the user session will wait for the BUILD operation to complete before returning success.

***Example*** for Standalone Deployment

```sql
BUILD DATABASE TO STANDBY (standby1_name, standby2_name) DISCONNECT FROM SESSION;
```

***Example*** for YAC Deployment

```sql
-- Execute on the master cluster
BUILD DATABASE TO STANDBY (standby1_name) DISCONNECT FROM SESSION;
```
