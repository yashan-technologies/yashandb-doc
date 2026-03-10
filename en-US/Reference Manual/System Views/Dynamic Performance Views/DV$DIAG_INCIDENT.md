Fault Diagnosis View, displaying event information for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER   | Group ID                                   |
| GROUP_NODE_ID        | INTEGER   | Node ID within the group                   |
| INCIDENT_ID          | BIGINT    | Event ID                                   |
| PROBLEM_ID           | INTEGER   | Problem ID associated with the event       |
| SESSION_ID           | INTEGER   | Session ID that created the event          |
| CREATE_TIME          | DATE      | Date and time the event was created       |
| FLOOD_CONTROL_NUMBER  | INTEGER   | Number of events under flood control       |
| ERROR_NUMBER         | INTEGER   | Error number for the current event         |
| ERROR_ARGUMENT       | VARCHAR(128)| Error parameters associated with the event |
| ERROR_COMMENTS       | VARCHAR(256)| Error description for the event           |
| ERROR_MESSAGE        | VARCHAR(512)| Error message from the database when the event occurred |