This view displays global archived slice file cleaning information, used to view the archived slice file cleaning status of all instances and the archiving cleaning LFN information of each instance in a cluster environment.

| Field         | Type    | Description               |
|---------------|---------|---------------------------|
| GROUP_ID      | NUMBER  | Group ID                  |
| GROUP_NODE_ID | NUMBER  | Node ID within the group  |
| INST_ID       | NUMBER  | Instance ID               |
| ARCH_INST_ID  | TINYINT | Archiving instance ID     |
| CLEAN_LFN     | BIGINT  | Cleaned log file number   |
| ARCH_MIN_LFN  | BIGINT  | Minimum archived log file number |