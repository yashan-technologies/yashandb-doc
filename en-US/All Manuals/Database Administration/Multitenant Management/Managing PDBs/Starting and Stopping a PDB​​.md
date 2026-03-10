This chapter mainly introduces PDB-level startup and shutdown operations, and all operations require the CDB root to be in OPEN state. For CDB-level startup and shutdown, please refer to the corresponding guidance:

- In Standalone Deployment: CDB startup and shutdown is equivalent to [instance startup and shutdown](../../Instance Management/Instance Startup and Shutdown).

- In YAC/Distributed Cluster Deployment: CDB startup and shutdown is equivalent to [cluster startup and shutdown](../../Cluster Management/Cluster Startup and Shutdown).

## Introduction to Startup and Shutdown Processes and Operating Modes

### Three Stages of PDB Startup

A PDB needs to go through the following stages from shutdown startup to normal use:  

*   **NOMOUNT**: Start the database instance, read the parameter file, but do not load the database; only the sys user is allowed to log in.

*   **MOUNT**: Start the database instance, read the control file, load the database, but the database is in a closed state; only the sys user is allowed to log in. PDBs in YAC/Distributed Cluster Deployment do not have this stage.  

*   **OPEN**: Start the database instance, load and open the database. During the database opening, the [database operating mode](#open_mode) can also be optionally selected as READWRITE, READONLY, or RESETLOGS.

<span id="open_mode" name="open_mode" class="yaslink"></span>

### Database Operating Mode

When the PDB moves to the OPEN stage, it supports four open modes: READWRITE, READONLY, RESETLOGS, and UPGRADE.

- **READWRITE**: The database defaults to opening in READWRITE mode. In this mode, the database supports full transactional read/write operations and is used in formal production environments.

- **READONLY**: The database is opened in read-only mode, restricting the database to read-only operations without generating any redo. PDB seed defaults to and can only be opened in READONLY mode. In primary-standby deployment, physical standby databases default to opening in READONLY mode.  

- **UPGRADE**: The upgrade tool *yasboot* opens the database in this mode during the upgrade process. In this mode, new session connections are not allowed, and manual OPEN operations are also not permitted.

- **RESETLOGS**: When a PITR (Point In Time Recovery), database flashback, or logical standby database configuration has occurred, and if a complete recovery cannot be performed, the database must be opened in RESETLOGS mode. This mode will reset the redo log sequence number.



### Closing Modes

YashanDB supports closing the PDB in the following three modes:

*   NORMAL: Wait for the completion of transactions before closing the PDB, with no waiting time limit. It is recommended to choose this mode to close the database.

*   IMMEDIATE: Forcefully interrupt all database operations, roll back uncompleted transactions, and close the database after waiting for dirty pages to be flushed.

*   ABORT: Forcefully interrupt all database operations and close the database without waiting for dirty pages to be flushed. This closing mode will increase the startup time as it does not wait for dirty pages to be flushed.

  > **Caution**: 
  >
  > It is only recommended to use the ABORT mode when the server crashes, loses power, or forcefully shuts down the database; otherwise, this mode should be avoided.



## Starting a PDB

When starting a PDB, its startup stages can be specified sequentially or skipped from NOMOUNT -> MOUNT -> OPEN, but direct rollback is not possible.

PDB can be started using the following methods:

| Methods   | Description                 |
| -------------------------------- | -------------------------------------------- |
| [*yasboot*](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot pdb)                                                | Can start PDB to any stage and can start batch PDBs, but cannot specify the PDB's open mode. |
| Execute [ALTER PLUGGABLE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PLUGGABLE DATABASE) statement in the CDB root | Can start PDB to any stage and can start batch PDBs, but cannot specify the PDB's open mode. |
| Execute [ALTER DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE) statement in the target PDB       | Can adjust the PDB from NOMOUNT stage to MOUNT stage or OPEN stage, but ALTER DATABASE statement only takes effect on the executing PDB. |
| [*ycsctl*](../../../Tools Guide/ycsctl/Node Management Commands)                                                   | Only applicable to the CDB in YAC/Distributed Cluster Deployment.  <br/>Can start PDB to any stage, but cannot specify the PDB's open mode. <br/>Only start the target PDB instance on the executing server. |

<span id="nomount" name="nomount" class="yaslink"></span>

### Start to NOMOUNT Stage

::: tabs
== Using *yasboot*

Log in to the database server as the database installation user, and use *yasboot* to start the PDB to the NOMOUNT stage. The command is as follows:  

```shell
# Scenario 1: Start PDB from closed state to NOMOUNT stage
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2 -m nomount
# Scenario 2: One-click restart PDB from any state to NOMOUNT stage  
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2 -m nomount
```

== Execute SQL statements in the CDB root

Log in to the CDB root using a common DBA user and execute the ALTER PLUGGABLE DATABASE statement to start the PDB to the NOMOUNT stage.

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 NOMOUNT;
```

== Using *ycsctl*

Only applicable to the CDB in YAC/Distributed Cluster Deployment.

Log in to the database server as the database installation user, and use *ycsctl* to start the PDB to the NOMOUNT stage. The command is as follows:  

```shell
# Start the PDB from closed state to NOMOUNT stage
$ ycsctl start pdb -db yashancdb -pdb pdb1 -m nomount
```
:::

After PDB is started to the NOMOUNT stage, STATUS becomes STARTED, which can be viewed by connecting to the CDB root or directly connecting to the target PDB.  

```shell
# Connect to the CDB root
$ yasql sys/********@192.168.1.2:1688
# Connect to the target PDB directly
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             STARTED
                    3 PDB2                                                             STARTED
```

### Start to MOUNT Stage

PDB can be started directly from closed state to MOUNT stage, or gradually started from NOMOUNT stage to MOUNT stage. The operation methods are divided into the following types:

::: tabs
== Using *yasboot*

```shell
# Scenario 1: Start PDB from closed state or NOMOUNT stage to MOUNT stage
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2 -m mount
# Scenario 2: One-click restart PDB from any state to MOUNT stage  
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2 -m mount
```

== Execute SQL statements in the CDB root

The target PDB can only be started from closed state or NOMOUNT phase to MOUNT phase.

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 MOUNT;
```

== Execute SQL statements in the target PDB

The target PDB must be in NOMOUNT stage.

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> ALTER DATABASE MOUNT;
```

== Using *ycsctl*

Only applicable to the CDB in YAC/Distributed Cluster Deployment.

```shell
$ ycsctl start pdb -db yashancdb -pdb pdb1 -m mount
```
:::

After PDB is started to the MOUNT stage, STATUS becomes MOUNTED, which can be viewed by connecting to the CDB root or directly connecting to the target PDB.  

```sql
show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             MOUNTED
                    3 PDB2                                                             MOUNTED
```

### Start to OPEN Stage

PDB can be started directly from closed state to MOUNT stage, or gradually started from NOMOUNT stage to MOUNT stage. The operation methods are divided into the following types:

::: tabs
== Using *yasboot*

When using yasboot tool to start PDB to OPEN, its running mode cannot be specified.  

```shell
# Scenario 1: Start PDB from closed state, NOMOUNT stage, or MOUNT stage to OPEN stage
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1,pdb2
# Scenario 2: One-click restart PDB from any state to OPEN stage  
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1,pdb2
```

== Execute SQL statements in the CDB root

When executing SQL statements to start PDB to OPEN by connecting to the CDB root, its running mode cannot be specified.  

The target PDB must not be in OPEN state.

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 OPEN;
```

== Execute SQL statements in the target PDB

The target PDB must be in NOMOUNT or MOUNT stage.

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> ALTER DATABASE OPEN READWRITE;
```

== Using *ycsctl*

Only applicable to the CDB in YAC/Distributed Cluster Deployment.

```shell
$ ycsctl start pdb -db yashancdb -pdb pdb1
```
:::

After PDB is started to the NOMOUNT stage, STATUS becomes OPEN, which can be viewed by connecting to the CDB root or directly connecting to the target PDB.  

```shell
# Connect to the CDB root
$ yasql sys/********@192.168.1.2:1688
# Connect to the target PDB directly
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN
                    3 PDB2                                                             OPEN 
```

<span id="close" name="close" class="yaslink"></span>

## Closing a PDB

PDB can be closed using the following methods:

| Methods   | Description                 |
| -------------------------------- | -------------------------------------------- |
| [*yasboot*](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot pdb)                                                | Can close PDB PDBs individually or in batch, but cannot use ABORT closing mode in Standalone Deployment, and cannot specify closing mode in YAC/Distributed Cluster Deployment. |
| Execute [ALTER PLUGGABLE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PLUGGABLE DATABASE) statement in the CDB root | Can close PDB PDBs individually or in batch, but cannot close PDBs in ABORT mode. |
| Execute [SHUTDOWN](../../../Development Guide/SQL Reference Manual/SQL Statements/SHUTDOWN) statement in the target PDB       | Only takes effect on the executing PDB. |
| [ycsctl](../../../Tools Guide/ycsctl/Node Management Commands)                                                   | Only applicable to the CDB in YAC/Distributed Cluster Deployment.  <br/>Can close 1 PDB instance at a time, but cannot specify the PDB's closing mode. <br/>Only close the target PDB instance on the executing server. |

::: tabs
== Using *yasboot*

When using *yasboot* to close PDBs, ABORT mode cannot be specified in Standalone Deployment, and all closing mode cannot be specified in YAC/Distributed Cluster Deployment.  

```shell
# Not specifying closing mode
$ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb1,pdb2

# In Standalone Deployment,closing mode can be specified as NORMAL or IMMEDIATE. If omitted, it defaults to NORMAL 
$ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb1,pdb2 -s normal
```

== Execute SQL statements in the CDB root

When executing SQL statements to start PDB to OPEN by connecting to the CDB root, the closing mode can only be specified as NORMAL or IMMEDIATE. If omitted, it defaults to NORMAL.  

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER PLUGGABLE DATABASE pdb1,pdb2 CLOSE;
```

== Execute SQL statements in the target PDB

When executing SQL statements to close PDB by directly connecting to the target PDB, any closing mode can be specified. If omitted, it defaults to NORMAL.  

```shell
$ yasql sys/********@192.168.1.2:1688/pdb1

SQL> SHUTDOWN NORMAL;
SQL> SHUTDOWN IMMEDIATE;
SQL> SHUTDOWN ABORT;
```

== Using *ycsctl*

Only applicable to the CDB in YAC/Distributed Cluster Deployment, and cannot specify the PDB's closing mode.

```shell
$ ycsctl stop pdb -db yashancdb -pdb pdb1
```
:::

## PDB Instance Load in YAC/Distributed Cluster Deployment

In YAC/Distributed Cluster Deployment, each PDB can run multiple instances (i.e., 0-1 instances per server) to concurrently read and write the same data. Based on server resource conditions and high availability requirements for specific PDBs, the instance distribution of each PDB can be load-balanced to avoid resource waste.

- Use the ycsctl stop pdb command to close an instance of a specific PDB on a specific server as needed.

- Use the ycsctl start pdb command to start an instance of a specific PDB on a specific server as needed.
