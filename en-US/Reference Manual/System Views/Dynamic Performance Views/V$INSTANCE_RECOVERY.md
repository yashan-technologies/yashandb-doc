This view displays information about the most recent instance recovery task.

|Field |Type |Description |
| --- | --- | --- |
| IN\_RECOVERY | BOOLEAN | Whether it is in failure recovery |
| INSTANCES | BIGINT | List of recovery instances, a 64-bit integer value that needs to be converted to binary for viewing. In binary, each bit represents the status of an instance (corresponding to the order of INST_ID). If an instance fails, the corresponding bit in INSTANCES is 1; if the instance is normal, it is 0. |
| BEGIN\_TIME | TIMESTAMP(6) | The start time of the instance recovery |
| GLOBAL\_RESOURCE\_AVAILABLE\_TIME | TIMESTAMP(6) | The time when global resources are available |
| COMPLETE\_RECOVERY\_TIME | TIMESTAMP(6) | The time when the instance recovery is fully available |
| END\_TIME | TIMESTAMP(6) | The end time of the instance recovery |
| RECOVERY\_REDO\_SIZE | BIGINT | The amount of redo involved in instance recovery (unit: bytes) |
| COMPLETE\_REDO\_SIZE | BIGINT | The amount of redo that has been completed for the current instance recovery (unit: bytes) |
| PHASE | VARCHAR(68) | The current phase of failure recovery |
| TARGET\_MTTR | INTEGER | The expected database recovery time is derived from the parameter FAST_START_MTTR_TARGET, unit: seconds |    
| ESTIMATED\_MTTR | INTEGER | The estimated time required to perform database recovery, unit: seconds |
