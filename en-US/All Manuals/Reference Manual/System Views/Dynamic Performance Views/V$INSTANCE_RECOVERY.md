This view displays information about the most recent instance recovery task.

|Field |Type |Description |
| --- | --- | --- |
| IN_RECOVERY | BOOLEAN | Whether it is in failure recovery |
| INSTANCES | BIGINT | List of recovery instances, a 64-bit integer value that needs to be converted to binary for viewing. In binary, each bit represents the status of an instance (corresponding to the order of INST_ID). If an instance fails, the corresponding bit in INSTANCES is 1; if the instance is normal, it is 0. |
| BEGIN_TIME | TIMESTAMP | The start time of the instance recovery |
| END_TIME | TIMESTAMP | The end time of the instance recovery |
| RECOVERY_REDO_SIZE | BIGINT | The amount of redo involved in instance recovery (unit: bytes) |
| COMPLETE_REDO_SIZE | BIGINT | The amount of redo that has been completed for the current instance recovery (unit: bytes) |
| GLOBAL_RESOURCE_AVAILABLE_TIME | TIMESTAMP | The time when global resources are available |
| COMPLETE_RECOVERY_TIME | TIMESTAMP | The time when the instance recovery is fully available |
| PHASE | VARCHAR(68) | The current phase of failure recovery |