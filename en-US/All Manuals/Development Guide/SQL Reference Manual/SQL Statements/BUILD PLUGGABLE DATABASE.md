## General Description

BUILD PLUGGABLE DATABASE is used to build standby PDBs in primary-standby high-availability deployment environments.



The applicable scope for this statement is as follows:

- This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE) and can only be executed when connected to the CDB root.

- This statement is not applicable to ISC Distributed Cluster Deployment.



Executing this statement requires the CDB root to be in OPEN state and the target PDB to be in closed state.

When executing the BUILD operation, the system will first perform an automatic environmental pre-check. The checks include whether the file path to be restored is accessible and whether there is enough free disk space at the target address. The BUILD will only be executed if the checks are passed; if the checks fail, the process will exit directly.

## Statement Definition

**build pluggable database::=**

```ebnf+diagram
syntax::= BUILD PLUGGABLE DATABASE ( (pdb_name) {"," (pdb_name)}|ALL) [SKIP VALIDATE] [OVERWRITE] [PARALLELISM integer]
```

### 1. pdb\_name|ALL

Specify the PDB(s) to be operated on. Multiple names should be separated by commas (`,`). Specifying ALL indicates operating on all PDBs.

Current PDB information can be obtained through the [V$PDBS](../../../Reference Manual/System Views/Dynamic Performance Views/V$PDBS) views or the `show pdbs` command in *yasql*.  



### 2. SKIP VALIDATE

Used to skip default disk, file, and other checks. Specific check items include whether the file paths to be restored on the target standby database are accessible, whether there is sufficient disk space for restoration, and whether files with the same name already exist.

### 3. OVERWRITE

Used to overwrite files with the same name. The overwrite process first deletes the existing files with the same name and then rebuilds new files at the original location.

Regardless of whether this keyword is specified, redo files and archive files will never be overwritten. If such files with the same name exist, they must be manually moved or cleared.

### 4. PARALLELISM integer

Specifies the level of parallelism for the BUILD PLUGGABLE DATABASE operation, with integer values ranging from `[1,16]`, with a default parallelism level of 4.



***Example*** for Standalone Deployment

```sql
BUILD PLUGGABLE DATABASE pdb1;
```
