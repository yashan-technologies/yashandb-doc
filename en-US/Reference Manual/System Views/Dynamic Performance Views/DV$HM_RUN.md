Fault Diagnosis View displays all health check-related information and their status for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER    | Group ID                             |
| GROUP_NODE_ID  | INTEGER    | Node ID within the group            |
| RUN_ID         | INTEGER    | Unique ID identifying the run       |
| NAME           | VARCHAR(33)| Unique name identifying the run     |
| CHECK_NAME     | VARCHAR(33)| Name of the check item              |
| RUN_MODE       | VARCHAR(8) | Running method <br>\*   MANUAL: Manually execute advanced package<br>\*   REACTIVE: Automatically triggered by the database |
| START_TIME     | DATE       | Start time of the run               |
| END_TIME       | DATE       | End time of the run                 |
| STATUS         | VARCHAR(11)| Running status <br>\*   INITIAL: Initializing file<br>\*   EXECUTING: Checking in progress<br>\*   ERROR: Exception during check<br>\*   COMPLETED: Check complete |
| NUM_INCIDENT   | INTEGER    | Number of incidents created by this run |
| ERROR_NUMBER    | INTEGER    | Error number (causing the run to fail) |