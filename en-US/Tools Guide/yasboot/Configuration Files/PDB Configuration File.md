PDB configuration file lists the configuration information for newly added PDBs in CDB.

When using yasboot to deploy YashanDB as a CDB, this file will be generated in the $YASDB_HOME directory.


If you need to plan a custom data file path for a newly created PDB, you must first complete the creation of the corresponding path (and permission configuration) before creating the PDB, and use file-name-convert to complete the path conversion when creating the PDB.

|Deployment Form  |Default PDB_DATA  |Requirements for Custom PDB_DATA  |
| ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| Standalone Deployment              | $YASDB_DATA/containers/{pdb_name} | Must be a local path, and the database installation user must have read-write permissions     |
| YAC/Distributed Cluster Deployment | +DG0/containers/{pdb_name}        |  Must be a [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) path    |

> **Note**:
>
> If you need to use the originally planned custom PDB_DATA when retrying after the PDB creation fails, you must also ensure that the target path is empty before retrying.



***Example***: pdb_add.toml in Standalone Deployment

```toml
file-name-convert="'?/containers/PDB$SEED/dbfiles','?/pdbs/pdb1/dbfiles','?/containers/PDB$SEED/local_fs','?/pdbs/pdb1/local_fs'"        # PDB data file path conversion rules
user="sys_pdb1"                           # Name of the PDB's local user
password="sys_pdb1"                       # Password of the PDB's local user
default-table-space-file="userspdb1"      # Data file for the built-in USERS tablespace of PDB
default-table-space-size="128M"           # Capacity of the built-in USERS tablespace of PDB
isarchive="false"                         # Whether the PDB enables archive mode. If archive mode was already enabled when creating the CDB, this parameter takes no effect and the PDB's archive mode is fixed as enabled; if archive mode was not enabled when creating the CDB, this parameter can be custom configured.
```


***Example***: pdb_add.toml in YAC/Distributed Cluster Deployment

```toml
file-name-convert="'+DG0/containers/PDB$SEED/dbfiles','+DG1/pdb1/dbfiles'"       # PDB data file path conversion rules, must be specified as YFS path
user="sys_pdb1"                           # Name of the PDB's local user
password="sys_pdb1"                       # Password of the PDB's local user
default-table-space-file="userspdb1"      # Data file for the built-in USERS tablespace of PDB
default-table-space-size="128M"           # Capacity of the built-in USERS tablespace of PDB
isarchive="false"                         # Whether the PDB enables archive mode. If archive mode was already enabled when creating the CDB, this parameter takes no effect and the PDB's archive mode is fixed as enabled; if archive mode was not enabled when creating the CDB, this parameter can be custom configured.
```
