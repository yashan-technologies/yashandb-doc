This view shows the statistics of the distributed background push tasks.

|Field |Type |Description |
|---------------|-------------|-------------|
| ID             | BIGINT     | Push queue sequence number      |
| USER_NAME      | VARCHAR(64) | User who pushed the message     |
| DST_NODE       | INTEGER     | Target node for the push        |
| CREATE_TIME    | DATE        | Creation time of the push task  |
| COST_SECONDS    | INTEGER     | Duration already executed        |
| STATUS         | INTEGER     | Push status<br/>* 1: Pending push<br/>* 2: Pushing   |
| ERR_MSG        | VARCHAR(800)   | Error return message from the push node |