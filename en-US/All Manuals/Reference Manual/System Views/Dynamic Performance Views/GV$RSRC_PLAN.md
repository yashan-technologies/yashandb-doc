This view displays information about all active resource plans in a CDB.

|Field |Type |Description |
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | Group ID               |
| GROUP_NODE_ID | NUMBER       | Node ID in current group      |
| INST_ID       | NUMBER       | Instance ID                   |
| ID                         | BIGINT      | Resource plan ID (a unique number, consistent across database shutdowns and startups). This is also the data dictionary object ID. |
| NAME                       | VARCHAR(68) | Name of the resource plan |
| IS_TOP_PLAN                | VARCHAR(16) | Indicates whether the resource plan is the current top plan (TRUE) or whether the resource plan is a subplan of the current top plan (FALSE). <br>The current value is fixed to `TRUE`. |
| CPU_MANAGED                | VARCHAR(16) | Indicates whether to manage CPU usage (ON) or not to manage CPU usage (OFF). <br>The current value is fixed to `ON`. |
| CPU_SCOPE                  | VARCHAR(16) | Reserved for future use.<br>The current value is fixed to `SERVER_WIDE` . |
| INSTANCE_CAGING            | VARCHAR(16) | Indicates whether the instance is control by CPU_COUNT (ON).<br>The current value is fixed to `OFF`. |
| PARALLEL_SERVERS_ACTIVE    | INTEGER     | Active parallel thread count on the instance |
| PARALLEL_SERVERS_TOTAL     | INTEGER     | All parallel thread count on the instance |
| PARALLEL_EXECUTION_MANAGED | VARCHAR(32) | Compatibility only, and the current value is fixed to NULL. |
| CON_ID                     | BIGINT      | The ID of the container to which the data pertains. Possible values include:<br>- 0: This value is used for rows containing data that pertain to the entire CDB. This value is also used for rows in non-CDBs.<br>- 1: This value is used for rows containing data that pertain to only the root<br>- n: Where n is the applicable container ID for the rows containing data |
| DIRECTIVE_TYPE             | VARCHAR(32) | The type of directive used by this PDB:<br>- DEFAULT_DIRECTIVE: Use default plan directive<br>- PDB: A PDB directive<br>- PROFILE: A profile directive|
| SHARES                     | INTEGER     | Resource allocation for this PDB, expressed in shares |
| UTILIZATION_LIMIT          | INTEGER     | Maximum percentage of the container's resources allowed for this PDB |
| PARALLEL_SERVER_LIMIT      | INTEGER     | Maximum percentage of the parallel target the PDB can use |
| MEMORY_MIN                 | INTEGER     | The percentage of memory that is guaranteed to the PDB.<br>This percentage is based on the total amount of space allocated to the CDB. |
| MEMORY_LIMIT               | INTEGER     | The maximum percentage of memory that the PDB can use.<br>This percentage is based on the total amount of space allocated to the CDB. |
| PROFILE                    | VARCHAR(68) | The value of the DB_PERFORMANCE_PROFILE initialization parameter for this PDB. |
| CPU_COUNT                  | INTEGER     | Compatibility only, and the current value is fixed to NULL. |
| CPU_MIN_COUNT              | INTEGER     | Compatibility only, and the current value is fixed to NULL. |