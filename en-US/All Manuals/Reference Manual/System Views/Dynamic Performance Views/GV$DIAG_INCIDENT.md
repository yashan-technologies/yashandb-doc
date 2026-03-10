Fault Diagnosis View, displays information about all current events.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | NUMBER  | Group ID                                 |
| GROUP_NODE_ID        | NUMBER  | Node ID within the group                 |
| INST_ID              | NUMBER  | Instance ID                              |
| INCIDENT_ID          | BIGINT  | ID of the event                          |
| PROBLEM_ID           | INTEGER | Problem ID associated with the event     |
| SESSION_ID           | INTEGER | Session ID that created the event        |
| CREATE_TIME          | DATE    | Date and time the event was created      |
| FLOOD_CONTROL_NUMBER  | INTEGER | Number of events for flood control       |
| ERROR_NUMBER         | INTEGER | Error number of the current event        |
| ERROR_ARGUMENT       | VARCHAR(128) | Error parameters associated with the event |
| ERROR_COMMENTS       | VARCHAR(256) | Error description of the event           |
| ERROR_MESSAGE        | VARCHAR(512) | Database error message when the event occurred |