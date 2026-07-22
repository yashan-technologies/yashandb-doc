This view displays information about the YStream server.

|Field |Type |Description |
|-------|------|------|
| SERVER_ID      | INTEGER       | Service ID |
| SERVER_NAME    | VARCHAR(64)   | Service Name |
| STATUS         | VARCHAR(16)   | Service Status<br/>* CREATED: The service has been created but has not been started yet.<br/>* STARTED: The service has been started, but no clients are connected through the current instance.<br/>* RUNNING: The service has been started, and clients are connected through the current instance.<br/>* STOPPED: The service has been stopped. |
| NODE_ID        | VARCHAR(16)   | Node ID that started the service |
| CREATE_TIME    | TIMESTAMP     | Creation Date |
| START_SCN      | BIGINT        | Starting SCN for parsing |
| START_POINT    | VARCHAR(4096) | Starting log point for parsing |
| RESTART_POINT  | VARCHAR(4096) | Restart log point for parsing |
| RESTART_POSITION| VARCHAR(4096) | Restart position for parsing |
| CAPTURE_POINT  | VARCHAR(4096) | Parsed log point |
| CAPTURE_POSITION| VARCHAR(4096) | Parsed position |
| APPLIED_POSITION| VARCHAR(4096) | Client applied position |
| ERROR          | VARCHAR(1024) | The last error message for the service on the current instance. If the service has not run on the current instance or has not encountered an error, this field is empty. The error messages may differ across instances in a YAC. Please query the latest error message for the service on the instance where the service was last run. |
| TYPE           | VARCHAR(16)   | Type of service<br/>* API: Indicates the server created by the user through the advanced package.<br/>* LOGICAL STANDBY: Built-in server generated when a logical standby database starts applying. |