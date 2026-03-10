## General Description

DROP DATABASE is used to delete the current database and remove the following persistent files contained within the database:


- Data files
- Slice files
- Redo log files (can be retained by specifying the KEEP LOGFILE keyword)  
- Archive log file(s) (retained by default, can be deleted together by specifying the INCLUDING ARCHIVELOG keyword)  
- Control files
- Double write file (no such file in YAC/Distributed Cluster)  

> **Warn**:
>
> The DROP DATABASE statement cannot be rolled back. **Please proceed with caution**.  




When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), if this statement is used while connected to the CDB root, it indicates performing global operations, covering both the CDB root and all PDBs. If this statement is used while directly connected to a specific PDB, an error will occur. Only by connecting to the CDB root can the [DROP PLUGGABLE DATABASE](./DROP PLUGGABLE DATABASE) statement be executed to delete a PDB.



This statement is not applicable for ISC Distributed Cluster Deployment.


The usage requirements for this statement are as follows:

- The database control files must be intact (all control file members must exist and be valid).

- The database must be in the NOMOUNT stage, and only the SYS user can connect to the database in this stage.

- In YAC/Distributed Cluster Deployment, this statement must be executed on the master instance (where INSTANCE_ROLE = MASTER_ROLE in the [GV$INSTANCE](../../../Reference Manual/System Views/Dynamic Performance Views/GV$INSTANCE) view).

- In a CDB, when deleting the CDB root, it is also required that all PDBs (including PDB seed) have been deleted.

## Statement Definition

**drop database::=**

```ebnf+diagram
syntax::= DROP DATABASE [INCLUDING ARCHIVELOG | KEEP LOGFILE]
```

### 1. INCLUDING ARCHIVELOG

This statement determines whether to delete the database's archive log files, which defaults to not deleting them.

### 2. KEEP LOGFILE

This statement determines whether to retain redo log files. The default is not to retain.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
# Start the instance to NOMOUNT
$ yasboot cluster restart -c yashandb -m nomount

# Log in to the database as sys user
$ yasql / as sysdba

-- Drop the database
SQL> DROP DATABASE;
-- or
DROP DATABASE INCLUDING ARCHIVELOG;
-- or
DROP DATABASE KEEP LOGFILE;
```
