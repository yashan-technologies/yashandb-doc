This view displays the statistics of the background push tasks of distributed MN nodes.

|Field |Type |Description |
|-----------------| ----------- |------------|
| GROUP_ID        | INTEGER     | Group ID                    |
| GROUP_NODE_ID  | INTEGER     | Node ID within the group    |
| ID                | BIGINT      | Push queue sequence number   |
| USER_NAME       | VARCHAR(64) | User sending the push message|
| DST_NODE        | INTEGER     | Target node of the push     |
| CREATE_TIME     | DATE        | Push task creation time      |
| COST_SECONDS     | INTEGER     | Duration of execution        |
| STATUS           | INTEGER     | 1 pending push 2 pushing     |
| ERR_MSG         | VARCHAR(800) | Error message from the push node|