This view is used to query the TASK INFO information stored in the CM module of all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER | Group ID               |
| GROUP_NODE_ID  | INTEGER | Node ID within the group|
| TASK_TYPE      | VARCHAR(16) | Task name           |
| SEND_TIMES     | INTEGER | Number of sends        |
| TASK_INFO      | VARCHAR(256) | Task message        |
| TARGET_NODES   | VARCHAR(128) | Target nodes        |