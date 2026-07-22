This view displays the statistics of background push tasks in ISC Distributed Cluster Deployment.

|Field |Type |Description |
|---------------|-------------|-------------|
| GROUP_ID       | NUMBER        | Group ID             |
| GROUP_NODE_ID  | NUMBER        | Node ID within the group |
| INST_ID        | NUMBER        | Instance ID          |
| ID             | BIGINT        | Push queue sequence number |
| USER_NAME      | VARCHAR(64)   | User who pushed the message |
| DST_NODE       | INTEGER       | Target node for the push |
| CREATE_TIME    | DATE          | Push task creation time |
| COST_SECONDS    | INTEGER       | Duration already executed |
| STATUS         | INTEGER       | Push status<br/>* 1: Pending<br/>* 2: In Progress |
| ERR_MSG        | VARCHAR(800)  | Error message returned from the push node |