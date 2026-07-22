## General Description

DROP PLUGGABLE DATABASE is used to delete the current database and remove the persistent files contained within the database:

- Data files
- Slice files
- Redo log files (can be retained by specifying the KEEP LOGFILE keyword)  
- Archive log file(s) (retained by default, can be deleted together by specifying the INCLUDING ARCHIVELOG keyword)  
- Control files
- Double write file (no such file in YAC/Distributed Cluster)  

> **Warn**:
>
> The DROP PLUGGABLE DATABASE statement cannot be rolled back. **Please proceed with caution**.  

The applicable scope for this statement is as follows:

- This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE) and can only be executed when connected to the CDB root.

- This statement is not applicable to ISC Distributed Cluster Deployment.

Executing this statement requires the CDB root to be in OPEN state and the target PDB to be in closed state.

## Statement Definition

**drop pluggable database::=**

```ebnf
= DROP PLUGGABLE DATABASE (pdb_name|ALL) [INCLUDING ARCHIVELOG | KEEP LOGFILE] [KEEP METADATA].
```

### pdb_name

Specify the name of the PDB to be deleted. Only one PDB can be specified per operation.

### ALL

Specifying this keyword indicates deletion of all PDBs, including the built-in PDB seed.  

>**Warn**:
>
> The CDB root must have one PDB seed. Currently, custom creation of PDB seed is not possible. Therefore, after executing `DROP PLUGGABLE DATABASE ALL`, the database will become unusable. **Currently, this operation is only intended as a preparatory step for deleting the CDB root. Proceed with caution.**  

When specifying ALL for deletion operations, partial PDB operation failures will not block the deletion of other PDBs, but error messages will be prompted.

### INCLUDING ARCHIVELOG

This statement determines whether to delete the database's archive log files, which defaults to not deleting them.

### KEEP LOGFILE

This statement determines whether to retain redo log files. The default is not to retain.

### KEEP METADATA

This statement determines whether to retain the metadata of the PDB. The default is not to retain.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- 1. Check PDB status  
show pdbs;

-- 2. Close the target PDB
ALTER PLUGGABLE DATABASE pdb1 CLOSE IMMEDIATE;

-- 3. Delete the target PDB
DROP PLUGGABLE DATABASE pdb1;
-- or
DROP PLUGGABLE DATABASE pdb1 INCLUDING ARCHIVELOG;
-- or
DROP PLUGGABLE  DATABASE pdb1 KEEP LOGFILE;
```
