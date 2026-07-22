This view displays information about currently running JOBs.

|Field |Type |Description |
| --- | --- | --- |
| SID | INTEGER    | Session ID executing the JOB                  |
| JOB | BIGINT     | JOB ID, automatically assigned by the system, same as OBJECT_ID in DBA_OBJECTS        |
| FAILURES | INTEGER    | Total number of JOB execution failures from JOB creation to querying the view           |
| LAST_DATE | DATE       | Date when the JOB was last executed successfully                         |
| LAST_SEC | VARCHAR(8) | Time when the JOB was last executed successfully                     |
| THIS_DATE | DATE       | Date when the JOB started execution                             |
| THIS_SEC | VARCHAR(8) | Time when the JOB started execution                           |
| INSTANCE | INTEGER    | Instance ID in the cluster where the JOB can execute. Default is 0, meaning it can execute on any instance                        |