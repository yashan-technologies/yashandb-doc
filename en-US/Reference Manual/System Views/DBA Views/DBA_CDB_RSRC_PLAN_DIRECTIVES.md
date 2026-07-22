This view displays information about all CDB resource plan directives.

This view is meaningful only when YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE).

|Field |Type |Description |
|---------------|--------------|--------------------|
| PLAN                  | VARCHAR(64)   | Name of the CDB resource plan |
| PLUGGABLE_DATABASE    | VARCHAR(64)   | Name of the PDB managed by the current directive |
| PROFILE               | VARCHAR(64)   | Name of the CDB resource template | 
| DIRECTIVE_TYPE        | VARCHAR(32)    | The type of directive:<br>- DEFAULT_DIRECTIVE: CDB default resource plan directive<br>- PDB: CDB resource plan directive dedicated to the specified PDB<br>- PROFILE: CDB resource template directive |
| SHARES                | NUMBER        | CPU/IOPS usage share |
| UTILIZATION_LIMIT     | NUMBER        | Maximum CPU/IOPS usage limit (unit: %) |
| PARALLEL_SERVER_LIMIT | NUMBER        | Maximum parallel resource usage percentage (unit: %) |
| MEMORY_MIN            | NUMBER       | Minimum memory usage percentage (unit: %) |
| MEMORY_LIMIT          | NUMBER       | Maximum memory usage percentage (unit: %) |
| COMMENTS              | VARCHAR(2000) | Comment of the resource plan directives |
| STATUS                | VARCHAR(1)    | Compatibility only, and the current value is fixed to NULL. |
| MANDATORY             | VARCHAR(3)    | Is Mandatory Existence or not (Cannot be deleted): YES or NO|