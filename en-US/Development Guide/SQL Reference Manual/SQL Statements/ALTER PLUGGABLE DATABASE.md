## General Description

ALTER PLUGGABLE DATABASE is used to modify the properties of the PDB seed or PDBs.



The applicable scope for this statement is as follows:

- This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE) and can only be executed when connected to the CDB root.

- This statement is not applicable to ISC Distributed Cluster Deployment.



## Statement Definition

**alter pluggable database::=**

```ebnf
= ALTER PLUGGABLE DATABASE ( pdb_name {',' pdb_name}|ALL) (startup_clause|standby_database_clauses).
```

**[startup_clause](#startupclause)::=**

```ebnf
= NOMOUNT | MOUNT | OPEN [RESETLOGS] | CLOSE [NORMAL | IMMEDIATE].
```

**[standby_database_clause](#standbydatabaseclause)::=**

```ebnf
= SWITCHOVER | FAILOVER (RESET ID integer).
```

### pdb_name|ALL

Specify the PDB(s) to be operated on. Multiple names should be separated by commas (`,`). Specifying ALL indicates operating on all PDBs.

Current PDB information can be obtained through the [GV$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/GV$PDBS)/[V$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/V$PDBS) views or the `show pdbs` command in *yasql*.  

<span id="startupclause" name="startupclause"></span>

### startup_clause

This statement is used to start or close PDB(s).

When specifying ALL to start all PDBs, the PDB seed is not included; when specifying ALL to close all PDBs, the PDB seed is included. When performing startup/shutdown operations with ALL specified, partial PDB operation failures will not block the startup/shutdown of other PDBs, but error messages will be prompted.

#### NOMOUNT

Start the target PDB to the NOMOUNT stage.

#### MOUNT

Start the target PDB to the MOUNT stage.

#### OPEN

Start the target PDB to the OPEN stage.

**RESETLOGS**

When a PITR (Point In Time Recovery), database flashback, or logical standby database configuration has occurred, and if a complete recovery cannot be performed, the database must be opened in RESETLOGS mode. This mode will reset the redo log sequence number.

> **Caution**: 
>  
> - When using the `ALL` keyword to operate on all PDBs, the `RESETLOGS` mode cannot be used to open the PDB.
>
> - If the PDB is opened in RESETLOGS mode while performing a complete recovery operation, it will generate error YAS-02184 and shut down the instance.

#### CLOSE

Shut down the target PDB. The shutdown option can be omitted, defaulting to NORMAL.

- **NORMAL**: Must wait for existing sessions to end before executing shutdown. 

- **IMMEDIATE**: Close the PDB as quickly as possible, without waiting for existing sessions to end, but will roll back uncommitted transactions.

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
ALTER PLUGGABLE DATABASE pdb1 MOUNT;
 
ALTER PLUGGABLE DATABASE pdb1 OPEN;

ALTER PLUGGABLE DATABASE pdb1 CLOSE;
```

<span id="standbydatabaseclause" name="standbydatabaseclause"></span>

### standby_database_clause

This statement is used to switch between the primary and standby PDBs. 

PDB seed has no primary-standby, and this statement cannot and need not be executed on it.

#### SWITCHOVER

This statement is used to manually perform a planned switchover between primary and standby databases when the synchronization between them is normal.

The usage rules for this statement are as follows:

- Both primary and standby databases must be in OPEN state. 

- The redo transmission link between primary and standby databases is normal, and the standby databases log synchronization status is normal. The primary databases can query the V$ARCHIVE_DEST_STATUS view or the standby databases can query the V$REPLICATION_STATUS view to confirm the corresponding information.

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
ALTER PLUGGABLE DATABASE pdb1 SWITCHOVER;
```

<span id="failover" name="failover"></span>

#### FAILOVER

This statement is used to manually perform a failover of the standby database when automatic primary selection is not enabled and the primary database is abnormal, forcibly switching the standby database to become the primary database.

RESET ID is only used in failover statements automatically issued by yasom in arbitration primary election scenarios. **This option must not be manually specified**.  

The usage rules for this statement are as follows:

- The current standby database must be in OPEN state.

- The connection between the current standby database and the primary database has been disconnected, i.e., CONNECTION=DISCONNECTED in the V$REPLICATION_STATUS view of the standby database.

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
ALTER PLUGGABLE DATABASE pdb1 FAILOVER;
```
