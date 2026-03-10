This view displays the accumulated session time for various operations.

|Field |Type |Description |
|-------------|-------------|-------|
| GROUP_ID      | NUMBER     | Group ID      |
| GROUP_NODE_ID | NUMBER     | Node ID within the group |
| INST_ID       | NUMBER     | Instance ID   |
| SID           | SMALLINT   | Session ID    |
| STAT_ID       | INTEGER    | Statistic ID  |
| STAT_NAME     | VARCHAR(64)| Statistic Name<br>\*   DB TIME: Time spent executing database user-level calls (unit: microseconds)<br>\*   PARSE TIME ELAPSED: Total time spent parsing SQL statement (unit: microseconds)<br>\*   HARD PARSE ELAPSED TIME: Time spent on hard parsing SQL statement (unit: microseconds)<br>\*   SQL EXECUTE ELAPSED TIME: Time spent executing SQL statement (unit: microseconds) |
| VALUE         | BIGINT     | Statistic Value |