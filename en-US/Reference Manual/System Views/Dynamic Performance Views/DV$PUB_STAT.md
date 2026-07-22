This view displays the statistics of the background push tasks of distributed MN nodes.

|Field |Type |Description |
|-----------------| ----------- |------------|
| GROUP\_ID        | INTEGER     | Group ID                    |
| GROUP\_NODE\_ID  | INTEGER     | Node ID within the group    |
| ID                | BIGINT      | Push queue sequence number   |
| USER\_NAME       | VARCHAR(64) | User sending the push message|
| DST\_NODE        | INTEGER     | Target node of the push     |
| CREATE\_TIME     | DATE        | Push task creation time      |
| COST\_SECONDS     | INTEGER     | Duration of execution        |
| STATUS           | INTEGER     | 1 pending push 2 pushing     |
| ERR\_MSG         | VARCHAR(800) | Error message from the push node|