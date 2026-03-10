This view displays a summary of all configuration parameters.

|Field |Type |Description |
| --- | --- | --- |
| NAME           | VARCHAR(64)   | The name of the parameter            |
| VALUE          | VARCHAR(4096) | The current value of the parameter in memory |
| DEFAULT_VALUE  | VARCHAR(4096) | The default value of the parameter   |
| IS_DEPRECATED   | VARCHAR(8)    | Whether the parameter is deprecated   |
| ISPDB_MODIFIABLE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter can be modified in PDB<br/>* TRUE: indicates that the parameter can be directly modified in PDB<br/>* FALSE: indicates that the parameter can only be modified by connecting to the CDB root, i.e., the parameter is a global parameter |
| ISPDB_PRIVATE | VARCHAR(5) | This field is only meaningful in a CDB<br/>Whether the parameter is a private parameter, i.e., whether it is an independent parameter for each container (the CDB root, PDB). Modifying private parameters only takes effect on themselves |