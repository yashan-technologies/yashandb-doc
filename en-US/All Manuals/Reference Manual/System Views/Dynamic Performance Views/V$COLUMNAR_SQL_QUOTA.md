This view shows the quota information used in the vectorization calculation process of SQL.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| SQL_ID             | VARCHAR(13)   | The ID value uniquely identifying a SQL statement |
| ESTIMATE            | BIGINT        | Estimated quota size           |
| LOW_LEVEL          | BIGINT        | Quota lower limit              |
| UP_LEVEL           | BIGINT        | Quota upper limit              |
| FREE_UP_LEVEL     | BIGINT        | Maximum value of reserved quota |
| MAX_USED           | BIGINT        | Maximum used quota              |
| USED                | BIGINT        | Currently used quota            |