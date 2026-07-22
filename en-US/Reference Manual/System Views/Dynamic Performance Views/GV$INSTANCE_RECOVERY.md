This view displays information about the latest instance recovery task.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| IN_RECOVERY | BOOLEAN | Whether it is in failure recovery |
| INSTANCES | BIGINT | List of recovery instances, 64-bit integer value, need to be converted to binary for viewing; in binary, each bit represents the status of an instance (corresponding to the order of INST_ID). If an instance fails, the corresponding INSTANCES bit value is 1; if the instance is normal, it is 0. |
| BEGIN_TIME | TIMESTAMP(6) | Time when instance recovery started |
| GLOBAL_RESOURCE_AVAILABLE_TIME | TIMESTAMP(6) | Time when global resources are available |
| COMPLETE_RECOVERY_TIME | TIMESTAMP(6) | Time when the instance recovery is fully available |
| END_TIME | TIMESTAMP(6) | Time when instance recovery ended |
| RECOVERY_REDO_SIZE | BIGINT | Amount of redo involved in instance recovery (unit: bytes) |
| COMPLETE_REDO_SIZE | BIGINT | Amount of redo that has been completed in the current instance recovery (unit: bytes) |
| PHASE | VARCHAR(68) | The current phase of failure recovery |