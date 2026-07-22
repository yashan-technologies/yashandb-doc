Fault diagnosis view, displaying all health check related information and its status.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER  | Group ID                             |
| GROUP_NODE_ID| NUMBER  | Node ID within the group            |
| INST_ID      | NUMBER  | Instance ID                          |
| RUN_ID       | INTEGER | Unique ID representing the run      |
| NAME         | VARCHAR(33) | Unique name to identify the run  |
| CHECK_NAME   | VARCHAR(33) | Name of the check item           |
| RUN_MODE     | VARCHAR(8) | Running mode <br>\*   MANUAL: Manually execute advanced packages <br>\*   REACTIVE: Automatically triggered by the database |
| START_TIME   | DATE    | Start time of the run               |
| END_TIME     | DATE    | End time of the run                 |
| STATUS       | VARCHAR(11) | Run status <br>\*   INITIAL: Initializing files <br>\*   EXECUTING: Currently executing check <br>\*   ERROR: An error occurred during the check <br>\*   COMPLETED: Completed |
| NUM_INCIDENT | INTEGER | Number of incidents created by this run |
| ERROR_NUMBER | INTEGER | Error number (run cannot be completed due to an error) |