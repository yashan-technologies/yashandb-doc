This view displays information about JOB objects created by the DBMS_JOB package.

|Field |Type |Description |
| :---------- |:-------------------------| :------------------------------------------------------ |
| JOB          | BIGINT                   | The JOB ID, automatically assigned by the system, same as OBJECT_ID in DBA_OBJECTS |
| LOG_USER     | VARCHAR(64)              | The user logged in when the JOB was created            |
| PRIV_USER    | VARCHAR(64)              | The user who granted the JOB privilege                  |
| SCHEMA_USER  | VARCHAR(64)              | The username of the JOB owner                           |
| LAST_DATE    | TIMESTAMP(6) WITH TIME ZONE | The last execution time of the JOB                      |
| LAST_SEC     | VARCHAR(8)               | Same as LAST_DATE                                       |
| THIS_DATE    | TIMESTAMP(6) WITH TIME ZONE | If the JOB is currently executing, this is the start time of the current execution. Usually NULL |
| THIS_SEC     | VARCHAR(8)               | Same as THIS_DATE                                       |
| NEXT_DATE    | TIMESTAMP(6) WITH TIME ZONE | The next execution time of the JOB                      |
| NEXT_SEC     | VARCHAR(8)               | Same as NEXT_DATE                                       |
| TOTAL_TIME   | INTEGER                  | The duration of the last execution (in seconds)         |
| MVIEW_CREATE | VARCHAR(5)               | Whether the JOB was internally generated during materialized view creation (TRUE/FALSE) |
| BROKEN       | CHAR(1)                  | Indicates whether the JOB is broken (Y/N)              |
| INTERVAL     | VARCHAR(4000)            | The expression used to calculate NEXT_DATE             |
| FAILURES     | INTEGER                  | The total number of failures in JOB execution from creation to query period |
| WHAT         | VARCHAR(4000)            | The action performed by the JOB                         |
| INSTANCE     | INTEGER                  | The instance ID in the cluster where the JOB can be executed. Default value is 0, indicating it can run on any instance |
| RUNNING_INSTANCE | INTEGER              | Shows the instance ID that is executing the JOB in the background, displays 0 if not in RUNNING state |