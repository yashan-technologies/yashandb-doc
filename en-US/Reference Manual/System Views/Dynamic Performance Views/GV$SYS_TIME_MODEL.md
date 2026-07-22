This view displays the accumulated time for various system operations.

|  Field          | Type       |  Description   |
|-------------|-------------|-------|
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID    | NUMBER      | Instance ID |
| STAT_ID    | INTEGER     | Statistic ID |
| STAT_NAME  | VARCHAR(64) | Statistic Name<br>\*   DB TIME: The time spent executing user-level database calls (unit: microseconds)<br>\*   connection management call elapsed time: The time spent on session connection and disconnection calls (unit: microseconds)<br>\*   PARSE TIME ELAPSED: Total time spent parsing the SQL statement (unit: microseconds)<br>\*   HARD PARSE ELAPSED TIME: Time spent hard parsing the SQL statement (unit: microseconds)<br>\*   SQL EXECUTE ELAPSED TIME: Time spent executing the SQL statement (unit: microseconds)<br>\*   PL/SQL execution elapsed time: Time spent executing PL (unit: microseconds)<br>\*   PL/SQL compilation elapsed time: Time spent compiling PL (unit: microseconds)<br>\*   repeated bind elapsed time: Time spent assigning new values to bind variables (re-binding) (unit: microseconds)<br>\*   Tablespace encryption elapsed time: Time spent on tablespace encryption and decryption (unit: microseconds) |
| VALUE       | BIGINT      | Statistic Value   |