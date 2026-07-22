This view shows the quota information used in the vectorization calculation process of SQL.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| SQL\_ID             | VARCHAR(13)   | The ID value uniquely identifying a SQL statement |
| ESTIMATE            | BIGINT        | Estimated quota size           |
| LOW\_LEVEL          | BIGINT        | Quota lower limit              |
| UP\_LEVEL           | BIGINT        | Quota upper limit              |
| FREE\_UP\_LEVEL     | BIGINT        | Maximum value of reserved quota |
| MAX\_USED           | BIGINT        | Maximum used quota              |
| USED                | BIGINT        | Currently used quota            |