This view displays the accumulated session time for various operations.

|Field |Type |Description |
|-------------|-------------|-------|
| SID         | SMALLINT    | Session ID                         |
| STAT_ID     | INTEGER     | Statistic Item ID                  |
| STAT_NAME   | VARCHAR(64) | Statistic Item Name<br>\*   DB TIME: The time spent executing user-level database calls (unit: microseconds)<br>\*   PARSE TIME ELAPSED: Total time spent parsing the SQL statement (unit: microseconds)<br>\*   HARD PARSE ELAPSED TIME: Time spent hard parsing the SQL statement (unit: microseconds)<br>\*   SQL EXECUTE ELAPSED TIME: Time spent executing the SQL statement (unit: microseconds) |
| VALUE       | BIGINT      | Statistic Value                    |