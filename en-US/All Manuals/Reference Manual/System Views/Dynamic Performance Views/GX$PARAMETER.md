This view displays a summary of the [hidden parameters](../../Hidden Configuration Parameters) for all nodes.

|Field |Type |Description |
|---------------|---------------|-----------|
| GROUP_ID       | NUMBER        | Group ID                     |
| GROUP_NODE_ID  | NUMBER        | Node ID within the group     |
| INST_ID        | NUMBER        | Instance ID                  |
| NAME           | VARCHAR(64)   | Name of the parameter        |
| VALUE          | VARCHAR(4096) | Parameter value for the current session |
| DEFAULT_VALUE  | VARCHAR(4096) | Default value of the parameter|
| IS_DEPRECATED   | VARCHAR(8)    | Whether the parameter is deprecated |
| ISPDB_MODIFIABLE | VARCHAR(5) | Whether the parameter can be modified in PDBs. **TRUE** indicates that the parameter can be modified directly within a Pluggable Database (PDB) and **FALSE** indicates that the parameter must be modified at the CDB$ROOT.   |
| ISPDB_PRIVATE | VARCHAR(5) | Modifying PDB-specific parameters only affects the current Pluggable Database (PDB) and does not impact other PDBs |