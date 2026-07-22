Fault Diagnosis View, displaying the information of all current events.

|Field |Type |Description |
| --- | --- | --- |
| INCIDENT_ID          | BIGINT  | The ID of the event                       |
| PROBLEM_ID           | INTEGER | The ID of the problem associated with the event |
| SESSION_ID           | INTEGER | The session ID that created the event     |
| CREATE_TIME          | DATE    | The date and time the event was created   |
| FLOOD_CONTROL_NUMBER  | INTEGER | The number of events under flood control   |
| ERROR_NUMBER         | INTEGER | The error number of the current event      |
| ERROR_ARGUMENT       | VARCHAR(128) | The error parameters associated with the event |
| ERROR_COMMENTS       | VARCHAR(256) | The error description of the event       |
| ERROR_MESSAGE        | VARCHAR(512) | The error message from the database at the time of the event generation |