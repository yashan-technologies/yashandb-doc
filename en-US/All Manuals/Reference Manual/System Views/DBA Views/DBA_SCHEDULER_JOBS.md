This view displays the JOB object information created by the DBMS_SCHEDULER advanced package.

|Field |Type |Description |
| ------------------- |------------------------------|-------------------------------------------------------------------------------------|
| OWNER               | VARCHAR(64)                  | The owner of the JOB                                                                      |
| JOB_NAME            | VARCHAR(64)                  | The name of the JOB                                                                       |
| JOB_SUBNAME         | VARCHAR(64)                  | Reserved field                                                                            |
| JOB_STYLE           | CHAR(7)                      | The type of the JOB, currently only supports REGULAR                                       |
| JOB_CREATOR         | VARCHAR(64)                  | The username that created the JOB                                                         |
| CLIENT_ID           | VARCHAR(64)                  | Reserved field                                                                            |
| PROGRAM_OWNER       | VARCHAR(4000)                | Reserved field                                                                            |
| PROGRAM_NAME        | VARCHAR(4000)                | Reserved field                                                                            |
| JOB_TYPE            | VARCHAR(16)                  | The type of JOB Action:<br>\* PLSQL_BLOCK<br>\* STORED_PROCEDURE                          |
| JOB_ACTION          | VARCHAR(4000)                | The action executed by the JOB                                                             |
| NUMBER_OF_ARGUMENTS | INTEGER                      | The number of parameters for the JOB                                                       |
| SCHEDULE_OWNER      | VARCHAR(4000)                | Reserved field                                                                            |
| SCHEDULE_NAME       | VARCHAR(4000)                | Reserved field                                                                            |
| SCHEDULE_TYPE       | CHAR(5)                      | Reserved field                                                                            |
| START_DATE          | TIMESTAMP(6) WITH TIME ZONE     | The time when the JOB starts executing                                                     |
| REPEAT_INTERVAL     | VARCHAR(4000)                | The interval at which the JOB executes                                                     |
| END_DATE            | TIMESTAMP(6) WITH TIME ZONE     | The time when the JOB ends executing                                                       |
| ENABLED             | BOOLEAN                      | Whether the JOB is enabled                                                                 |
| AUTO_DROP           | VARCHAR(5)                   | Whether the JOB is automatically deleted after completion (TRUE/FALSE)                     |
| MVIEW_CREATE        | VARCHAR(5)                   | Whether the JOB is internally generated when creating a materialized view (TRUE/FALSE)   |
| STATE               | VARCHAR(9)                   | The current state of the JOB:<br>\* DISABLED<br>\* SCHEDULED<br>\* RUNNING<br>\* SUCCEEDED<br>\* BROKEN<br>\* FAILED |
| RUN_COUNT           | INTEGER                      | The number of times the JOB has been executed                                              |
| MAX_RUNS            | INTEGER                      | The maximum number of times the JOB can be executed                                      |
| FAILURE_COUNT       | INTEGER                      | The number of times the JOB has failed                                                    |
| MAX_FAILURES        | INTEGER                      | The number of failures allowed for the JOB. If exceeded, the JOB will be marked as invalid |
| RETRY_COUNT         | INTEGER                      | The number of retries after the JOB fails                                                  |
| LAST_START_DATE     | TIMESTAMP(6) WITH TIME ZONE     | The time when the JOB was last started                                                    |
| LAST_RUN_DURATION   | INTERVAL DAY(9) TO SECOND(6) | The duration of the last execution of the JOB (in seconds)                                |
| NEXT_RUN_DATE       | TIMESTAMP(6) WITH TIME ZONE     | The time when the JOB will next run                                                       |
| MAX_RUN_DURATION    | INTERVAL DAY(3) TO SECOND(0) | Reserved field                                                                            |
| COMMENTS            | VARCHAR(4000)                | User-added description for the JOB                                                       |
| INSTANCE_ID         | INTEGER                      | The instance ID where the JOB can be executed in the cluster. Default is 0, meaning it can run on any instance |
| RUNNING_INSTANCE    | INTEGER                      | Displays the instance ID that is executing the JOB in the background; shows 0 if not in RUNNING state. |