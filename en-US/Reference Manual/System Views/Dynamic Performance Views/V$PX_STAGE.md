This view displays the STAGE information of the current node.

|Field |Type |Description |
|----------------| --- | --- |
| SQL_ID         | VARCHAR(13) | SQL identifier      |
| STAGE_ID       | SMALLINT    | STAGE identifier    |
| STATUS         | VARCHAR(64) | STAGE status        |
| NEED_TRIGGER    | TINYINT     | Whether the STAGE needs to be triggered |
| FIRST_STAGE    | SMALLINT    | Identifier of the first STAGE that this STAGE depends on |
| START_TIME     | TIMESTAMP(6)   | STAGE start time    |