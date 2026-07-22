This view displays the accumulated session time for various operations.

|Field |Type |Description |
|-------------|-------------|-------|
| GROUP_ID      | NUMBER     | Group ID      |
| GROUP_NODE_ID | NUMBER     | Node ID within the group |
| INST_ID       | NUMBER     | Instance ID   |
| SID           | SMALLINT   | Session ID    |
| STAT_ID       | INTEGER    | Statistic ID  |
| STAT\_NAME  | VARCHAR(64) | Statistic name<br>\*   DB TIME: Time spent executing database user-level calls (unit: microseconds)<br>\*   connection management call elapsed time: Time spent executing session connection and disconnection calls (unit: microseconds)<br>\*   PARSE TIME ELAPSED: Total time spent parsing SQL statements (unit: microseconds)<br>\*   HARD PARSE ELAPSED TIME: Time spent hard parsing SQL statements (unit: microseconds)<br>\*   SQL EXECUTE ELAPSED TIME: Time spent executing SQL statements (unit: microseconds)<br>\*   PL/SQL execution elapsed time: Time spent executing PL (unit: microseconds)<br>\*   PL/SQL compilation elapsed time: Time spent compiling PL (unit: microseconds)<br>\*   repeated bind elapsed time: Time spent assigning new values to bind variables (re-binding) (unit: microseconds)<br>\*   Tablespace encryption elapsed time: Time spent encrypting and decrypting tablespaces (unit: microseconds) |
| VALUE       | BIGINT      | Statistic Value |