This view displays information about the scheduled tasks for auditing log cleanup.

|Field |Type |Description |
| ------------------- |------------------------------|----------------------------------------------------------------------------------------|
| OWNER               | VARCHAR(64)                   | The owner of the JOB                                                                    |
| JOB_NAME            | VARCHAR(64)                   | The name of the JOB                                                                     |
| JOB_SUBNAME         | VARCHAR(64)                   | Reserved field                                                                          |
| JOB_STYLE           | CHAR(7)                       | The type of the JOB, currently only REGULAR is supported                                |
| JOB_CREATOR         | VARCHAR(64)                   | The username that created the JOB                                                       |
| CLIENT_ID           | VARCHAR(64)                   | Reserved field                                                                          |
| PROGRAM_OWNER       | VARCHAR(4000)                 | Reserved field                                                                          |
| PROGRAM_NAME        | VARCHAR(4000)                 | Reserved field                                                                          |
| JOB_TYPE            | VARCHAR(16)                   | The type of the JOB Action:<br>\* PLSQL_BLOCK: Anonymous block<br>\* STORED_PROCEDURE: Stored procedure |
| JOB_ACTION          | VARCHAR(4000)                 | The action executed by the JOB                                                          |
| NUMBER_OF_ARGUMENTS | INTEGER                       | The number of parameters for the JOB                                                    |
| SCHEDULE_OWNER      | VARCHAR(4000)                 | Reserved field                                                                          |
| SCHEDULE_NAME       | VARCHAR(4000)                 | Reserved field                                                                          |
| SCHEDULE_TYPE       | CHAR(5)                       | Reserved field                                                                          |
| START_DATE          | TIMESTAMP WITH TIME ZONE      | The time when the JOB starts executing                                                  |
| REPEAT_INTERVAL     | VARCHAR(4000)                 | The time interval for JOB execution                                                      |
| END_DATE            | TIMESTAMP WITH TIME ZONE      | The time when the JOB finishes executing                                                |
| ENABLED             | BOOLEAN                       | Whether the JOB is enabled                                                              |
| AUTO_DROP           | VARCHAR(5)                    | Whether the JOB is automatically deleted after completion (TRUE/FALSE)                  |
| STATE               | VARCHAR(9)                    | The current state of the JOB:<br>\* DISABLED: Disabled<br>\* SCHEDULED: Scheduled<br>\* RUNNING: Running<br>\* COMPLETED: Completed |
| RUN_COUNT           | INTEGER                       | The number of times the JOB has been executed                                           |
| MAX_RUNS            | INTEGER                       | The maximum number of times the JOB can be executed                                     |
| FAILURE_COUNT       | INTEGER                       | The number of times the JOB has failed                                                  |
| MAX_FAILURES        | INTEGER                       | The number of allowed failures for the JOB; if exceeded, the JOB will be marked as disabled |
| RETRY_COUNT         | INTEGER                       | The number of retries for the JOB after failure                                          |
| LAST_START_DATE     | TIMESTAMP WITH TIME ZONE      | The time when the JOB last started executing                                             |
| LAST_RUN_DURATION   | INTERVAL DAY(9) TO SECOND(6) | The duration of the last execution of the JOB (in seconds)                              |
| NEXT_RUN_DATE       | TIMESTAMP WITH TIME ZONE      | The time when the JOB is scheduled to run next                                          |
| MAX_RUN_DURATION    | INTERVAL DAY(3) TO SECOND(0) | Reserved field                                                                          |
| COMMENTS            | VARCHAR(4000)                 | Descriptive information added to the JOB by the user                                    |