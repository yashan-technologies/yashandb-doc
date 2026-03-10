This view displays the quota information used in the vectorization calculation process PLAN.

|Field |Type |Description |
|-----------------|-------------|-----------------|
| SQL_ID          | VARCHAR(13) | The ID value that uniquely identifies a SQL statement |
| STAGE_ID        | INTEGER     | The ID of the STAGE in SQL      |
| PLAN_ID         | INTEGER     | The ID of the PLAN in SQL       |
| NAME             | VARCHAR(16) | The name of the PLAN in SQL     |
| ESTIMATE         | BIGINT      | Estimated quota size            |
| LOW_LEVEL       | BIGINT      | Quota lower limit               |
| UP_LEVEL        | BIGINT      | Quota upper limit               |
| DEGREE           | SMALLINT    | Degree of parallelism           |
| MIN_UP_LEVEL   | BIGINT      | Minimum quota upper limit of the operator |
| EXPEND           | BOOLEAN     | Whether the quota can be expanded |
| MAX_USED        | BIGINT      | Maximum used quota              |
| USED             | BIGINT      | Currently used quota             |