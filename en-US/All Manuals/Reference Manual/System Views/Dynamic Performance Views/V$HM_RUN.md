Fault Diagnosis View, displaying all health check related information and their status.

|Field |Type |Description |
| --- | --- | --- |
| RUN_ID       | INTEGER    | Represents the unique ID of the run          |
| NAME         | VARCHAR(33)| Used to identify the unique name of the run  |
| CHECK_NAME   | VARCHAR(33)| The name of the check item                   |
| RUN_MODE     | VARCHAR(8) | Run mode <br>\*   MANUAL: Manual execution of the advanced package <br>\*   REACTIVE: Automatically triggered by the database |
| START_TIME   | DATE       | The start time of the run                    |
| END_TIME     | DATE       | The end time of the run                      |
| STATUS       | VARCHAR(11)| Run status <br>\*   INITIAL: Initializing files <br>\*   EXECUTING: Executing checks <br>\*   ERROR: Check encountered an exception <br>\*   COMPLETED: Check completed |
| NUM_INCIDENT | INTEGER    | The number of incidents created by this run   |
| ERROR_NUMBER | INTEGER    | Error number (due to errors causing the run to be incomplete) |