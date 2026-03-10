This view displays all nodes' STAGE information.

|Field |Type |Description |
|----------------| --- | --- |
| GROUP_ID         | NUMBER   | Group ID                  |
| GROUP_NODE_ID    | NUMBER   | Node ID within the group  |
| INST_ID          | NUMBER   | Instance ID               |
| SQL_ID           | VARCHAR(13) | SQL identifier          |
| STAGE_ID         | SMALLINT | STAGE identifier          |
| STATUS           | VARCHAR(64) | STAGE status            |
| NEED_TRIGGER     | TINYINT  | Whether the STAGE needs triggering |
| FIRST_STAGE      | SMALLINT | The identifier of the first STAGE that this STAGE depends on |
| START_TIME       | TIMESTAMP(6) | STAGE start time         |