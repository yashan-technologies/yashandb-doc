Fault Diagnosis View, displaying information of all current health inspection items.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                 |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                 |
| INST_ID       | NUMBER  | Instance ID                              |
| ID            | INTEGER | Health Check ID                          |
| NAME          | VARCHAR(64) | Health Check Name                     |
| OFFLINE_CAPABLE | VARCHAR(3) | Can run when the database is not open, YES or NO |
| DESCRIPTION   | VARCHAR(1024) | Description of the check functionality |